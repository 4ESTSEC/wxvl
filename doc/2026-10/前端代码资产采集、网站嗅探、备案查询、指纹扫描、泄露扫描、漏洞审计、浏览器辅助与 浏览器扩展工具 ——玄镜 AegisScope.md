#  前端代码资产采集、网站嗅探、备案查询、指纹扫描、泄露扫描、漏洞审计、浏览器辅助与 浏览器扩展工具 ——玄镜 AegisScope  
 风铃Sec   2026-10-07 08:37  
  
声明：仅用于授权测试，用户滥用造成的一切后果和作者无关 请遵守法律法规！出于对安全考量本公众号发布的所有文章中的  
工具均建议放在虚拟机中运行  
！【无需回复关键字，文中第二部分0x02获取工具】  
  
**0x01 工具简介**  
  
玄镜 AegisScope 是一款面向授权安全测试、SRC 辅助审计和前端资产分析场景的浏览器扩展工具。插件围绕当前访问页面工作，将前端代码资产采集、网站技术嗅探、备案查询、主动指纹扫描、敏感信息泄露扫描、前端漏洞审计、API 批量测试、Vue Router 运行时分析和浏览器辅助能力整合到同一套工作台中。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/bhUibV7MPZluoAFaTsWweUSIqjgkAvhSR8IWWCXwOicdhXVxBbSJweBJV8ibqDTibB73vYB5q5tO6iaWUuvABW6XD0fWVXlDcA5nonhpXyiacdPuI/640?wx_fmt=jpeg "")  
##### 备案查询  
  
备案查询结果在主弹窗内展示。插件会识别当前域名，并从页面正文、源码和链接中提取 ICP 备案号、公安备案号、许可证线索和备案相关链接。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/bhUibV7MPZlvic4kjibJVKZib2x9yXcJFmm2Dy0wibx8qI0VqbQMxGteCRE5d90w7Mw60ftJsEZoicNWBtGLTNWL4ul4gtT31s51p2iaw9rlujLDnM/640?wx_fmt=png&from=appmsg "")  
##### 泄露扫描  
  
泄露扫描用于识别前端代码中的敏感信息和不安全写法，例如：  
- 云厂商密钥、平台 Token、Bearer / Basic / Cookie 凭证。  
  
- 邮箱、手机号、身份证号、银行卡号、公网 IP、内网 IP。  
  
- Slack、Discord、企业微信、钉钉、飞书等 Webhook。  
  
- 硬编码密钥、弱加密算法、危险解码执行。  
  
- Webpack、Vite、Next.js、Nuxt 等打包产物和混淆特征。  
  
扫描结果按风险等级排序，高风险内容优先展示。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/bhUibV7MPZlsDd8TwRl5HIpnOI4MsKjmpgZlicZnfQJGlm2rHyAVVWSlkfaVTVKm4nicF7tpBicpvbtYk8O0rBbBwLPibAmBkn4drT7kRErHibvsM/640?wx_fmt=png&from=appmsg "")  
##### 漏洞审计&API 批量测试  
  
漏洞审计会从前端源码和接口线索中提取潜在业务风险，常见方向包括：  
- 未授权接口、敏感接口、接口文档暴露。  
  
- 前端路由鉴权依赖客户端状态。  
  
- IDOR、越权、租户/角色/归属字段可控。  
  
- 支付、订单、金额、优惠、余额等逻辑参数风险。  
  
- DOM XSS、开放重定向、上传校验依赖前端、CSRF、CORS 风险。  
  
可安全自动验证的结果会显示“验证”按钮。需要业务上下文的结果会标记为“人工复核”，避免产生误导。  
API 批量测试位于漏洞审计模块内。支持 GET、POST、HEAD、OPTIONS、PUT、PATCH、DELETE，也支持自定义请求体、登录态/匿名对比、响应预览和单条接口手动测试。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/bhUibV7MPZlvnB99jvOSqbibuJymia9N7JGZfreLpicEWVvicjZKpE6my8IgxsjV203pbkwF99SOXOmSya2Y6lKoGvicb6Z9dQBaQlAibyGZegng9M/640?wx_fmt=png&from=appmsg "")  
##### UA修改  
  
UA 修改：用于快速切换当前页面 User-Agent。内置 66 个常用 UA，覆盖桌面浏览器、移动浏览器、App 内置浏览器、微信、支付宝、钉钉、飞书和搜索爬虫等场景。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/bhUibV7MPZlvo5MIE30ouCiayB895CRDCBibEKThUDqn8Ze7X1D9KOQaia7GEh5mqLIvvgOOVMvpfdSgunAM8FwsFX52lBNXuiclYnMjVcAibr454/640?wx_fmt=png&from=appmsg "")  
  
**0x03 下载链接**  
```
后台回复：20261007
获取下载链接
```  
  
