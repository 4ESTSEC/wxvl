#  护网出局单位的RCE  
原创 Xluo
                    Xluo  信息安全中转站   2026-09-20 09:57  
  
这家单位是某次护网行动出局的一家单位。  
  
业务数据停在半年前，13 个微服务还挂着，网关 7×24 裸奔。就是这种「应该注销却还活着」的资产，被拿到了 Windows SYSTEM。  
  
漏洞是公开的，payload 是现成的，触发条件网上也写得很清楚。**过程里真正费时间的，是一个卡点，也是本次分享的一个容易被忽视的思路：SpEL 执行不稳定，跟版本、跟窗口都没关系，根因是路由定义仓库被脏路由污染。**  
  
前置条件  
  
架构是 zhoutaoo/SpringCloud 脚手架改造的网关，Spring Cloud Gateway 3.1.1+，已经修过 CVE-2022-22947。后端 Windows，13 个微服务。  
  
CVE-2022-22947 的条件是：WebFlux 变体、依赖 Actuator、exposure.include 暴露 gateway 端点、端点未认证。这台机器 exposure.include=*，actuator 全裸，四条全中。  
  
heapdump 里能直接挖出 Redis 密码和认证白名单。Java 字符串默认 UTF-16 存储，解码后检索目标字符串，掩码配置在堆转储里反而全是明文。  
  
SpEL 在路由构建期执行  
  
利用链本身不新鲜。路由过滤器参数的 #{} 会被当成 SpEL 求值，sink 在 org.springframework.cloud.gateway.support.ShortcutConfigurable[#getValue]()  
。  
  
调用链是 RouteDefinitionRouteLocator[#convertToRoute]()  
 → loadGatewayFilters → ConfigurationService.AbstractBuilder[#bind]()  
 → normalizeProperties → getValue。POST 到 /actuator/gateway/routes 的路由定义，会在路由构建期被解析执行。  
  
refresh 发布 RefreshRoutesEvent，触发全量重建，重建时逐个转换定义，SpEL 就在这个阶段跑。时间盲测的原理也在这，sleep(8000) 塞进过滤器参数，refresh 被阻塞约 8 秒，就是执行成功的信号。  
  
补丁堵了「读」，没堵「写」  
  
3.1.1 修复时引入 GatewayEvaluationContext，禁止 T() 类型引用、构造函数、bean 引用。但赋值型 SpEL 依然合法，可以修改 Spring Environment 属性，这是 CVE-2025-41243，CVSS 10.0。  
  
#{@systemProperties['spring.cloud.gateway.restrictive-property-accessor']='false'}  
  
限制关掉，T() 复活，回到老路。实测 exec 路由不依赖 unlock 前置，unlock 路由经常 404 不持久化，但 exec 照样执行。  
  
两张表  
  
真正卡人的是执行不稳定。同样的 payload，refresh 有时 6 秒，有时 0.2 秒就完事，后者说明 SpEL 根本没执行。  
  
gateway 的 actuator 上有两张表。routes 是构建完成的 Route 视图，DELETE 即消失。routedefinitions 是 RouteDefinitionRepository 里的定义记录，只要 POST 添加过就永久保留。  
  
**关键在重建是全量的。refresh 把仓库里所有定义重新加载、逐个 convertToRoute，含 SpEL 的脏定义在受限上下文求值直接抛 SpelEvaluationException，异常向上传播，整批路由构建失败，新路由没有注册机会。**  
  
于是 SpEL 失效，refresh 0.2 秒空转，sleep 还没轮到求值，构建就断了。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/7xn5zj065lzvjOZxjuIb2HJbo6l11wHkRicfqkprerY6K74BpQsgNmnYMSXFciaiaNG51ibg5HtOt19vhATyzhc0kOP8QSXFiaTUH1rmzdnoV1b8/640?wx_fmt=png&from=appmsg "")  
  
污染机制与清理路径  
  
GET routedefinitions 一拉，54 条定义，其中 41 条是攻击者留下的。攻击本身就在污染路由源，而且越打越严重。  
  
时灵时不灵，是脏定义和新建路由在重建流里的时序竞争。这个现象跟「窗口期」长得太像，很容易往版本、时间窗口上归因。  
  
清理  
  
解法不复杂。routedefinitions 拉出来和 routes 对比，多出来的就是脏的，逐个 DELETE /actuator/gateway/routes/{id}，再 refresh。DELETE 会同步清掉仓库里的定义。  
  
54 → 13，同一套 payload 一次成功，whoami 返回 nt authority\system。  
  
所以routedefinitions 数量明显多于 routes 时，先清理再打，比写重试脚本快得多。  
  
路由也改成用完即删，一条路由只 refresh 一次，多 refresh 几次 Nacos 一覆盖，路由就失真了。一个目标被很多人打过时，里面可能混着别人的脏路由，那只能按命名删吧。  
  
误删业务路由，那就只能提桶跑路了（笑）。  
  
本文为经验分享，目标系统已上报整改，请勿用于未授权目标。  
  
