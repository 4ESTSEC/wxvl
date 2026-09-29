#  CNNVD | 人工智能重要漏洞通报（2026年第十四期）  
 中国信息安全   2026-09-29 09:43  
  
[](https://cisat.cn/all/14915419?from_tag=1)  
  
**漏洞情况**  
  
根据国家人工智能安全漏洞库（AIVD）统计，近期（2026年9月3日至2026年9月28日）共采集重要人工智能漏洞392个，AIVD对这些漏洞进行了收录。本周人工智能类漏洞主要涵盖了Google（Gemini）、FlowiseAI、OpenClaw等多个厂商（项目）。AIVD对其危害等级进行了评价，其中超危漏洞64个，高危漏洞193个，中危漏洞135个。  
  
## 一人工智能漏洞增长数量情况  
  
  
近期AIVD采集人工智能漏洞392个。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/uOZw5Efn8etV78YRknMXomIibqPCQbeFH77bpx3JhG6tgEicYn4ODMha615dAzffbneGNvgXEGrb7KfAaQ9Lib2ZEwg0GbKf5vlAvJmQsBRmQs/640?wx_fmt=png&from=appmsg&watermark=1#imgIndex=3 "")  
  
图1 近期漏洞新增数量统计图  
  
  
## 二人工智能漏洞具体情况  
  
  
近期共采集重要人工智能漏洞392个，包括Google（Gemini）、FlowiseAI、OpenClaw等多个厂商（项目）的漏洞。其中超危漏洞64个，高危漏洞193个，中危漏洞135个。具体如表1所示：  
  
表1 人工智能漏洞列表  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/uOZw5Efn8esuIJic79y1wWiaQke33HmvWU7VDMdro9U9KqLgbgiaVkNMylNpSzjyBNBJhbypZwNRKyuDrRdVyvhLfXOT49gaMiaElaAWPGdnYicY/640?wx_fmt=png&from=appmsg&watermark=1#imgIndex=4 "")  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/uOZw5Efn8euch5q3IiahOzEWVIAZyuiaGu1jN424ibzs4w4Plh2jFTf2RicFuCicPTLY4FAhUz1sHDLy1fdE6wia7xVrTnBBolypXGapV3ZOLef0s/640?wx_fmt=png&from=appmsg&watermark=1#imgIndex=5 "")  
  
## 三重要人工智能漏洞实例  
  
  
近期重要漏洞实例如表2所示。  
  
表2 本期重要漏洞实例  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/uOZw5Efn8esxFia3FFIFgfmTh5YEicvvQEqRansCiaIeQVvCNXyd7ibeAJt4S7VRAC5aUKlq0C8libsheRY7948pYyVRR4cNo2AbXg0KWibiaxDnuo/640?wx_fmt=png&from=appmsg&watermark=1#imgIndex=6 "")  
  
1. OpenClaw 加密问题漏洞（CNNVD-2026-34619595）  
  
OpenClaw是一个开源的智能人工助理。  
  
OpenClaw for iOS 2026.7.1版本至2026.8.11之前版本存在加密问题漏洞，该漏洞源于在控制界面中未强制执行已保存的网关TLS PIN码，攻击者利用该漏洞可以获取操作员权限并泄露凭据。  
  
目前厂商已发布升级补丁以修复漏洞，参考链接：  
  
[https://github.com/openclaw/openclaw/security/advisories/GHSA-jjpc-p3xf-8g7p](https://github.com/openclaw/openclaw/security/advisories/GHSA-jjpc-p3xf-8g7p)  
  
  
2. vLLM 资源管理错误漏洞（CNNVD-2026-98830536）  
  
vLLM是一个开源的适用于LLM的高吞吐量和内存高效推理和服务引擎。  
  
vLLM 0.28.0之前版本存在资源管理错误漏洞，该漏洞源于内存过度分配，攻击者利用该漏洞可以导致API服务器进程崩溃。  
  
目前厂商已发布升级补丁以修复漏洞，参考链接：  
  
[https://github.com/vllm-project/vllm/security/advisories/GHSA-99f2-hwrc-gvq8](https://github.com/vllm-project/vllm/security/advisories/GHSA-99f2-hwrc-gvq8)  
  
  
3. Ollama 服务端请求伪造漏洞（CNNVD-2026-94820222）  
  
Ollama是Ollama公司开源的一个可以在本地设备上运行、管理和自定义大语言模型的工具。  
  
Ollama 0.30.0版本至0.33.2版本存在服务端请求伪造漏洞，该漏洞源于在拉取tensor-layer模型时未能验证重定向目标，攻击者利用该漏洞可以获取敏感信息。  
  
目前厂商已发布升级补丁以修复漏洞，参考链接：  
  
[https://ollama.com/](https://ollama.com/)  
  
  
（来源：CNNVD）  
  
![](https://mmbiz.qpic.cn/mmbiz_png/LJwWAbW20CgcIwtJdXPBvfHMpzZGGcibotzrva8dnEORibzB3Sia00JnbxaGI7nfBeO0lezb37OSUjhe70w7icxEEdHT5gBPs8lIq5S885D1HiaU/640?wx_fmt=png&from=appmsg "")  
[](https://cisat.cn/)  
  
