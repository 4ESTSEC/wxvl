#  2026最新CTF知识库：12大Web漏洞深度文章+1156篇历年大赛WP+50+脚本+Payload速查 +AI/RAG离线在线知识库  
Dest1ny-Sec
                    Dest1ny-Sec  乌雲安全   2026-09-22 01:58  
  
CTF知识库介绍  
  
传统 CTF 知识库就是一堆 WP 链接散落各处，搜不到、找不到、用不顺。Des-CTF-Knowledge **把 1156 篇 WP 抽取为结构化 meta 元数据 + 12 类漏洞深度 + 386 场大赛索引**  
，天然适配大模型 RAG（AnythingLLM / Dify / RAGFlow 直接喂），是选手的第二大脑，也是 AI 的领域知识。  
- **1156 篇**  
 大赛 WP 抽取为 11 字段结构化 meta（title / contest / year / difficulty / vuln_type / tags / attack_chain / key_payload / one_liner / lesson / quality  
），结构统一，喂给 AnythingLLM / Dify / RAGFlow 当训练语料  
  
- **12 类**  
漏洞深度文章（SQL 注入 / RCE / SSRF / SSTI / JWT / 反序列化 等）给 AI 当领域知识  
  
- **386 场**  
大赛索引，按时间 / 难度 / 类型多维检索，强网杯 / 西湖论剑 / 网鼎杯 / HITCON / SECCON / N1CTF / RCTF / 长城杯 / 羊城杯 等  
  
![Des-CTF-Knowledge — CTF 选手的第二大脑](https://mmbiz.qpic.cn/mmbiz_jpg/5yYXmGfnscRtc88UryYIUDskWOx4kxNmVyVI37KqVL678VibRRwdmlGiceX7iaRqHHAoEsO1qwOOicEGGBLxWQRT3MmcwDf2ib6auXcia7Uv1RZTU/640?wx_fmt=jpeg&from=appmsg "")  
![]( "")  
![]( "")  
![]( "")  
# 2026年最新CTF知识库 — SQL注入·RCE·文件上传·文件包含·SSRF·SSTI·JWT·PHP反序列化·命令执行·隐写术·流量分析·密码学·压缩包攻击·Web安全漏洞总结 + 历年大赛WriteUp合集 + 离线在线AI大模型RAG知识库必备 + Payload速查 + 脚本工具  
> CTF选手的第二大脑 | The Ultimate CTF Knowledge Base for AI & Human  
  
## 本项目有什么  
  
这是一套**全网最完整的CTF中文知识体系**  
，内容覆盖从入门到国赛的方方面面：  
🧩 你需要的📦 这里都有漏洞原理学习SQL注入/RCE/文件上传/文件包含/SSRF/SSTI/JWT/PHP反序列化/PHP代码审计 — 12篇深度文章，累计2万+行历年大赛真题WP强网杯/西湖论剑/网鼎杯/HITCON/SECCON/N1CTF/RCTF/长城杯/羊城杯等386个大赛 — 1156篇结构化meta做题Payload速查10大类高频Payload：SQL注入/XSS/SSTI/命令执行/文件上传/XXE/SSRF/文件包含/JWT/反序列化现成解题脚本Base全家桶/RSA全场景攻击/CRC爆破/USB流量解析/盲水印/古典密码/steghide爆破等 — 50+脚本靶场通关WPctfshow全系列(命令执行/文件上传/反序列化/SSTI/php特性) + SQLI-LABS + upload-labs工具速查手册sqlmap / ffuf / Dirsearch / Wireshark / tshark / 内存取证AI知识库全文Markdown + 1156篇结构化meta，天然适配Claude/ChatGPT/AnythingLLM/Dify/RAGFlow等大模型RAG系统## 四种使用方式  
### 方式一：导入 AI 大模型做 RAG 知识库（⭐推荐）  
  
**这是本项目相比其他CTF资源的最大优势——所有内容都是 Markdown 纯文本，AI 可以直接理解。**  
```
# Step 1: 克隆到本地
git clone https://github.com/<your-username>/CTF-Knowledge-Base-2026.git

# Step 2: 导入你用的 AI 平台
```  
  
<table><thead><tr style="box-sizing: border-box;background-color: rgb(255, 255, 255);border-top: 1px solid rgba(209, 217, 224, 0.7);"><th style="box-sizing: border-box;padding: 6px 13px;font-weight: 600;border-color: rgb(209, 217, 224);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><section><span leaf="">AI 平台</span></section></th><th style="box-sizing: border-box;padding: 6px 13px;font-weight: 600;border-color: rgb(209, 217, 224);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><section><span leaf="">导入方法</span></section></th><th style="box-sizing: border-box;padding: 6px 13px;font-weight: 600;border-color: rgb(209, 217, 224);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><section><span leaf="">效果</span></section></th></tr></thead><tbody><tr style="box-sizing: border-box;background-color: rgb(255, 255, 255);border-top: 1px solid rgba(209, 217, 224, 0.7);"><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(209, 217, 224);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><strong style="box-sizing: border-box;font-weight: 600;margin-bottom: 0px;"><span leaf="">Claude Code / Claude Desktop</span></strong></td><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(209, 217, 224);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><section><span leaf="">直接设置工作目录为本项目，AI 自动读取 </span><code style="box-sizing: border-box;font-family: ui-monospace, SFMono-Regular, &#34;SF Mono&#34;, Menlo, Consolas, &#34;Liberation Mono&#34;, monospace;font-size: 13.6px;tab-size: 4;white-space: break-spaces;background-color: rgba(129, 139, 152, 0.12);border-radius: 6px;margin: 0px;padding: 0.2em 0.4em;"><span leaf="">AI-SEARCH-INDEX.md</span></code></section></td><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(209, 217, 224);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><section><span leaf="">🥇 最佳体验</span></section></td></tr><tr style="box-sizing: border-box;background-color: rgb(246, 248, 250);border-top: 1px solid rgba(209, 217, 224, 0.7);"><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(209, 217, 224);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><strong style="box-sizing: border-box;font-weight: 600;margin-bottom: 0px;"><span leaf="">ChatGPT</span></strong></td><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(209, 217, 224);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><section><span leaf="">对话中直接拖入 </span><code style="box-sizing: border-box;font-family: ui-monospace, SFMono-Regular, &#34;SF Mono&#34;, Menlo, Consolas, &#34;Liberation Mono&#34;, monospace;font-size: 13.6px;tab-size: 4;white-space: break-spaces;background-color: rgba(129, 139, 152, 0.12);border-radius: 6px;margin: 0px;padding: 0.2em 0.4em;"><span leaf="">.md</span></code><span leaf=""> 文件，或上传到 MyGPTs 知识库</span></section></td><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(209, 217, 224);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><section><span leaf="">单文件</span></section></td></tr><tr style="box-sizing: border-box;background-color: rgb(255, 255, 255);border-top: 1px solid rgba(209, 217, 224, 0.7);"><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(209, 217, 224);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><strong style="box-sizing: border-box;font-weight: 600;margin-bottom: 0px;"><span leaf="">AnythingLLM</span></strong></td><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(209, 217, 224);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><section><span leaf="">新建 Workspace → Upload → 选中本项目目录</span></section></td><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(209, 217, 224);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><section><span leaf="">全量索引</span></section></td></tr><tr style="box-sizing: border-box;background-color: rgb(246, 248, 250);border-top: 1px solid rgba(209, 217, 224, 0.7);"><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(209, 217, 224);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><strong style="box-sizing: border-box;font-weight: 600;margin-bottom: 0px;"><span leaf="">Dify / FastGPT</span></strong></td><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(209, 217, 224);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><section><span leaf="">知识库 → 导入文档 → 批量上传 </span><code style="box-sizing: border-box;font-family: ui-monospace, SFMono-Regular, &#34;SF Mono&#34;, Menlo, Consolas, &#34;Liberation Mono&#34;, monospace;font-size: 13.6px;tab-size: 4;white-space: break-spaces;background-color: rgba(129, 139, 152, 0.12);border-radius: 6px;margin: 0px;padding: 0.2em 0.4em;"><span leaf="">.md</span></code><span leaf=""> 文件</span></section></td><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(209, 217, 224);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><section><span leaf="">全量索引</span></section></td></tr><tr style="box-sizing: border-box;background-color: rgb(255, 255, 255);border-top: 1px solid rgba(209, 217, 224, 0.7);"><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(209, 217, 224);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><strong style="box-sizing: border-box;font-weight: 600;margin-bottom: 0px;"><span leaf="">RAGFlow</span></strong></td><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(209, 217, 224);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><section><span leaf="">创建知识库 → 上传文件夹 → 选择通用嵌入模型</span></section></td><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(209, 217, 224);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><section><span leaf="">全量索引</span></section></td></tr><tr style="box-sizing: border-box;background-color: rgb(246, 248, 250);border-top: 1px solid rgba(209, 217, 224, 0.7);"><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(209, 217, 224);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><strong style="box-sizing: border-box;font-weight: 600;margin-bottom: 0px;"><span leaf="">Cherry Studio</span></strong></td><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(209, 217, 224);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><section><span leaf="">知识库 → 导入 → 添加文件夹</span></section></td><td style="box-sizing: border-box;padding: 6px 13px;border-color: rgb(209, 217, 224);border-style: solid;border-width: 1px;border-image: none 100% / 1 / 0 stretch;"><section><span leaf="">全量索引</span></section></td></tr></tbody></table>  
```
# RAG 平台推荐分块配置
分块大小: 1000-1500 tokens
重叠: 200 tokens
索引模式: 高质量嵌入
检索 top_k: 5-8
```  
  
**AI 提问示例：**  
```
✅ "目标是一道PHP反序列化CTF题，已知存在wakeup绕过，帮我梳理完整利用链"
✅ "找一下2022-2025年所有涉及SQL二次注入的大赛WP"
✅ "给我一个文件上传.htaccess绕过的最新Payload"
✅ "这道题png图片打不开，帮我用CRC爆破修复宽高"
✅ "总结了什么SSRF打Redis的利用方式？"
```  
### 方式二：打CTF比赛时当场查  
```
# 进入项目目录
cd CTF-Knowledge-Base-2026/

# 快速定位：先看全局索引
cat AI-SEARCH-INDEX.md | grep -i "你遇到的问题"

# 按漏洞类型搜文章
grep -rn "SQL注入\|RCE\|SSRF" --include="*.md" .

# 搜Payload（最常用！）
grep -A 20 "SQL注入" PAYLOAD-CHEATSHEET.md

# 搜历年同类题目WP（meta格式）
grep -rl "反序列化\|wakeup" CTF大赛WP集合/articles/ --include="*.meta.md" | head -10

# 需要某个脚本，直接去对应目录
ls CTF常用脚本及工具/CRC32校验爆破/
python3 CTF常用脚本及工具/CRC32校验爆破/crc32.py
```  
### 方式三：系统学习Web安全  
  
按推荐顺序阅读核心文章：  
```
第一轮（基础）:
  1. SQL.md              → 联合注入 → 报错注入 → 盲注
  2. 命令执行.md          → system/passthru → 反弹shell → disable_functions绕过
  3. 文件上传漏洞.md       → 一句话木马 → MIME绕过 → 解析漏洞

第二轮（进阶）:
  4. 文件包含.md           → php伪协议 → 日志包含 → session包含
  5. SSRF漏洞.md           → gopher打Redis → FastCGI → DNS-rebinding
  6. php代码审计.md        → 弱类型 → 变量覆盖 → preg_match绕过

第三轮（高级）:
  7. PHP反序列化漏洞总结.md  → POP链 → phar → 字符逃逸 → 原生类
  8. SSTI.md              → Jinja2 → Flask → __subclasses__链
  9. JWT.md               → None算法 → 密钥爆破 → KID注入

Misc方向:
  10. 图片隐写.md          → LSB → 宽高爆破 → 盲水印
  11. 压缩包总结.md         → CRC爆破 → 明文攻击 → 伪加密
  12. 音频隐写.md           → 频谱 → DTMF → MP3Stego
```  
  
配合 WP汇总/  
 中的靶场WP动手实操，每学一个专题就去靶场打对应题目。  
### 方式四：搭建团队共享知识库  
```
# 1. 部署 Dify / RAGFlow（Docker一键部署）
docker-compose -f dify/docker-compose.yaml up -d

# 2. 创建团队知识库，批量导入本项目所有 .md 文件

# 3. 团队成员通过 Web UI 或 API 查询
curl -X POST http://your-dify/api/chat \
  -H "Authorization: Bearer $API_KEY" \
  -d '{"query": "如何绕过disable_functions执行命令？"}'

# 4. 新大赛WP持续积累——团队每打一场比赛就把WP加到对应目录
```  
## 完整目录结构  
```
CTF-Knowledge-Base-2026/
│
├── README.md                          # 你在看这个
├── AI-SEARCH-INDEX.md                 # ⭐ AI Agent 全局检索索引（AI优先读这个）
├── PAYLOAD-CHEATSHEET.md              # ⭐ 10大类高频Payload速查表（做题直接翻）
├── LICENSE                            # MIT
│
├── 【Web漏洞深度文章 — 12篇】
├── SQL.md                             # SQL注入 (2611行)
├── 命令执行.md                        # 命令执行/RCE (3463行)
├── 文件上传漏洞.md                    # 文件上传总结 (2115行)
├── 文件包含.md                        # 文件包含/LFI (2617行)
├── SSRF漏洞.md                        # SSRF (1882行)
├── PHP反序列化漏洞总结.md             # PHP反序列化 (6959行⭐最大)
├── php代码审计.md                     # PHP代码审计 (1641行)
├── SSTI.md                            # 服务器模板注入
├── JWT.md                             # JWT攻击
│
├── 【Misc方向文章 — 3篇】
├── 图片隐写.md                        # 图片隐写术
├── 音频隐写.md                        # 音频隐写术
├── 压缩包总结.md                      # 压缩包攻击
│
├── 【脚本工具 — 50+】
├── CTF常用脚本及工具/
│   ├── SCRIPTS-INDEX.md               #   脚本速查索引
│   ├── RSA综合脚本利用/               #   RSA全场景攻击脚本集
│   ├── Base/                          #   Base全家桶 (B16/B32/B64/B85/隐写/异或)
│   ├── CRC32校验爆破/                 #   CRC32爆破 1-5字节
│   ├── usb流量/                       #   USB键盘/鼠标流量解析
│   ├── TTL隐写/                       #   TTL值编解码
│   ├── 频域盲水印/                    #   图片频域盲水印
│   ├── 图片爆破宽高/                  #   PNG IHDR宽高CRC修复
│   ├── steghide爆破密码/              #   steghide字典爆破
│   ├── 字节转二维码/                  #   01矩阵→二维码
│   ├── RGB转图片/                     #   RGB元组→图片
│   ├── 文件异或/                      #   文件XOR操作
│   ├── 日志匹配/                      #   LFI日志包含后关键词提取
│   ├── 进制互相转换/                  #   多进制转换器
│   ├── 双参数爆破脚本/                #   Web双参数fuzz
│   ├── md5爆破/                       #   MD5带通配符还原
│   ├── 维吉尼亚加密/                  #   维吉尼亚加解密
│   ├── 变异凯撒/                      #   凯撒密码变体
│   ├── ... (40+更多)
│   └── python-note.md                 #   Python CTF常用代码笔记
│
├── 【历年大赛WP — 1156篇（结构化 meta）】
├── CTF大赛WP集合/
│   └── articles/                      #   386个大赛的1156篇 meta 元数据
│       ├── 强网杯_xxx.meta.md         #   ← 按大赛名+题名命名，后缀 .meta.md
│       ├── 西湖论剑_xxx.meta.md
│       ├── HITCON_xxx.meta.md
│       ├── ...（共1156个 .meta.md 文件）
│
├── 【靶场WP — 10+套】
├── WP汇总/
│   ├── ctfshow-命令执行wp.md          #   ctfshow命令执行全系列
│   ├── ctfshow-文件上传wp.md          #   ctfshow文件上传全系列
│   ├── CTfshow-反序序列化wp.md        #   ctfshow反序列化全系列
│   ├── ctfshow-SSTI.md                #   ctfshow SSTI系列
│   ├── ctfshow-php特性.md             #   ctfshow PHP特性系列
│   ├── 各大靶场WP汇总.md              #   SQLI-LABS等
│   ├── ssti入门知识点.md              #   SSTI入门到进阶
│   ├── Nodejs/                         #   Nodejs原型链污染
│   ├── XSS/                            #   XSS专项
│   ├── 代码审计/                       #   代码审计专项
│   ├── 文件包含/                       #   文件包含专项
│   └── 文件上传/                       #   文件上传专项
│
└── 【工具速查 — 6篇】
    └── 工具使用/
        ├── sqlmap-Cheat-Sheet.md       #   sqlmap命令速查
        ├── ffuf的使用.md               #   ffuf fuzz速查
        ├── Dirsearch.md                #   Dirsearch目录扫描
        ├── Wireshark使用.md            #   Wireshark流量分析
        ├── tshark使用.md               #   tshark命令行分析
        └── 内存取证秒杀所有命令.md      #   Volatility内存取证
```  
## 关键词地图（搜什么都能找到）  
```
Web漏洞:    SQL注入 · MySQL注入 · 联合注入 · 报错注入 · 堆叠注入 · 盲注 · 布尔盲注 ·
            时间盲注 · 宽字节注入 · 二次注入 · DNS注入 · WAF绕过 · 命令执行 · RCE ·
            反弹shell · 无回显 · disable_functions · 无字母RCE · 文件上传 ·
            WebShell · 一句话木马 · 图片马 · .htaccess · 解析漏洞 · 条件竞争 ·
            文件包含 · LFI · RFI · php伪协议 · php://filter · php://input ·
            data:// · expect:// · 日志包含 · session包含 · 临时文件包含 ·
            SSRF · gopher协议 · dict协议 · Redis未授权 · FastCGI · DNS-rebinding ·
            SSTI · Flask · Jinja2 · Twig · Smarty · PHP反序列化 · POP链 ·
            phar反序列化 · wakeup绕过 · 字符逃逸 · 原生类 · SoapClient ·
            JWT · None算法 · 密钥爆破 · KID注入 · JKU · XSS · CSRF · XXE ·
            Nodejs原型链污染 · PHP代码审计 · 弱类型 · MD5碰撞 · preg_match绕过

Misc/隐写:  图片隐写 · LSB隐写 · PNG结构 · IHDR · GIF隐写 · JPG隐写 · 盲水印 ·
            频域水印 · 音频隐写 · 频谱 · 波形 · DTMF · SSTV · MP3Stego · SilentEye ·
            压缩包攻击 · ZIP明文攻击 · CRC爆破 · 伪加密 · RAR · 流量分析 ·
            pcap · USB流量 · TTL隐写 · 曼彻斯特编码 · 内存取证 · Volatility

密码学:     RSA · AES · DES · MD5 · SHA · Base64 · Base32 · Base16 · Base85 ·
            凯撒密码 · 维吉尼亚密码 · 四方密码 · Nihilist · rot13 · Brainfuck ·
            键盘密码 · 进制转换 · 异或 · 古典密码

逆向:       Reverse · xor · 文件异或 · APP逆向 · jadx · 二进制

综合:       CTF · Capture The Flag · 网络安全 · 信息安全 · 渗透测试 · Web安全 ·
            WriteUp · WP · 靶场 · ctfshow · 知识库 · RAG · AI大模型 · LLM
```  
## 常见问题  
  
**Q: 这么多文件，AI 能全部读完吗？**  
 A: AI 不会一次性读全部文件。AI-SEARCH-INDEX.md  
 是索引文件（仅250行），AI 先读它定位到具体文章，再按需读取。大文件（如 PHP反序列化 6959行）配有 .idx.md  
 索引，AI 可以用 Read offset=行号 limit=行数  
 精准分段读取。  
  
**Q: 和其他的CTF知识库有什么区别？**  
 A: ① 全 Markdown 格式，天然适配 AI/RAG 系统 ② 1156篇结构化 meta（11 字段 schema），比原文长文更适合 RAG 分块 ③ 知识文章带 .idx.md  
 分段索引，AI友好 ④ 50+ 脚本工具即开即用  
  
**Q: v1.0.0 和 v2.0.0 有什么区别？应该选哪个？**  
 A:  
版本内容大小适用场景v1.0.0 (2026-08-18)1156 篇原文 WP 长文35MB需要详细攻击步骤/完整代码/原始截图时v2.0.0 (2026-08-29)1156 篇结构化 meta（11 字段）5.4MBAI/RAG 检索、做题速查、空间敏感时  
两个版本在 GitHub Releases 页面都可下载，按需取用。  
  
**Q: meta 里的字段够用吗？想看详细 Payload/脚本怎么办？**  
 A: meta 涵盖 title/contest/year/difficulty/vuln_type/tags/attack_chain/key_payload/one_liner/lesson/quality 11 字段，足以支持绝大多数"找思路/找参考"场景。如果你需要某个题目的**完整代码/详细步骤/原始截图**  
，可以：  
1. 去 GitHub Releases 下载 v1.0.0  
 的 zip  
  
1. 在 Trash 里找 v2 转换前的 1156 篇原文（用 mavis-trash  
 移动的，可恢复）  
  
1. 在 articles/  
 目录按 meta 标题搜索对应的原文  
  
**Q: 文章内容更新到什么时候？**  
 A: 核心漏洞文章覆盖经典技术，meta 抽取自 ctfiot.com 2025-12 前的 WP，2026年新比赛会持续更新。  
  
**Q: 我是新手，应该从哪里开始？**  
 A: 建议看上方「方式三：系统学习Web安全」的推荐阅读顺序，从 SQL注入 开始，配合靶场WP动手练习。  
  
项目地址  
  
https://github.com/Dest1ny-Sec/Des-CTF-Knowledge  
  
  
