#  安全工具丨AI驱动的CVE漏洞全自动挖掘平台，一键挖掘CVE漏洞、代码审计、漏洞验证、出报告，全流程自动化！  
原创 蓝星安全
                    蓝星安全  蓝星安全   2026-07-02 22:00  
  
         
**点击上方****蓝星安全****关注我**  
  
****  
  
**免责声明：本公众号分享的任何资料仅限用于安全学习，严禁用于其他用途，请严格遵守中华人民共和国法律法规，对因不遵守国家法律法规而产生的任何后果，均由个人自行承担，本公众号不承担任何责任！**  
  
****  
**获取资料，请扫码下方二维码加入知识星球**  
  
![](https://mmbiz.qpic.cn/mmbiz_png/opKHaHXLxcmxg12rU33pwcICNRkiaof5YSUAGfWPApU7M1BfdsTTOyvREw0gKD42g4U8eefG4n0XuYETEtbxSIN2drACnNVz3UeicCcldM5AA/640?wx_fmt=png&from=appmsg "")  
## 一、简介  
  
     AutoCVE是一个**Agent驱动的自动化CVE发现平台**  
，旨在实现从项目筛选、源码审计、漏洞验证到CVE报告生成的全流程自动化  
[citation:1]  
。简单来说，用户只需要提供目标项目或筛选条件，AutoCVE便能像一支训练有素的“安全研究员团队”一样，自动完成后续的挖掘工作。AutoCVE在为期一周的测试中，共同发现并提交了**30个安全漏洞！**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/opKHaHXLxckLbS6VV6C5DAWghPCRwFiaibl1INuXSg6NwE8uftD4qVlO8WtJYo8O6q1mibtfNONGS7lp16iaAk57e4Mus357SibfqyDKDlwF7WwM/640?wx_fmt=png&from=appmsg "")  
## 二、核心能力  
  
AutoCVE之所以能实现高效的自动化漏洞挖掘，主要得益于其以下几项核心能力：  
### 2.1. 一键式自动化流水线  
  
AutoCVE最显著的特点是将零散的安全研究步骤整合为一条完整的自动化流水线  
[citation:1]  
。用户仅需简单配置，平台便能自动完成：  
- **项目筛选与导入**  
  
- **审计任务创建**  
  
- **Agent驱动的漏洞挖掘**  
  
- **CVE申报报告生成**  
  
用户最终只需复制报告内容并提交，即可完成CVE申请，极大降低了漏洞挖掘的技术门槛和重复劳动  
[citation:1]  
。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/opKHaHXLxckleEghJmSjVY8icicmWoMOABN5pClb1PdaPSHX2ZRtp5q82TwHqBmbKibmjV90rgHBxuYAKQOAn1iayxCvLUIY6Gt5QLvMViaaQPOc/640?wx_fmt=png&from=appmsg "")  
### 2.2. Multi-Agent协同审计机制  
  
AutoCVE并非依赖单一AI模型，而是通过**Orchestrator（编排器）**  
 统一调度多个专业化Agent协同工作  
[citation:1]  
。这些Agent各司其职，构成了一个完整的审计流水线：  
- **Recon Agent**  
：负责信息收集。  
  
- **Scan Agent**  
：执行工具扫描。  
  
- **Triage Agent**  
：进行误报过滤。  
  
- **Finding Agent**  
：专注漏洞深度挖掘。  
  
- **Verification Agent**  
：负责动态验证。  
  
这种多Agent协作模式模仿了人类安全团队的的分工，提升了审计的全面性和准确性  
[citation:1]  
。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/opKHaHXLxcnL0edibuhj58QM7Zq3BblVlr9Mq6aDbH4kKLXAs7DGR9FWuUHSZFtOiazKxZEQLhpjxiamqhBsnXqePicUIer5VeVINGYI8ibmVqws/640?wx_fmt=png&from=appmsg "")  
### 2.3. 三种灵活的审计模式  
  
针对不同的审计目标，AutoCVE提供了三种可选的审计模式，兼顾效率与深度  
[citation:1]  
：  
- **增强扫描**  
：快速分析工具扫描结果并过滤误报，适合快速排查。  
  
- **智能审计**  
：由Finding Agent主导，深度挖掘高价值漏洞，专注于CVE和0Day研究。  
  
- **综合审计**  
：融合工具扫描与源码分析，进行全量审计，覆盖面最广。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/opKHaHXLxcm2Vyiasz89ZrpSKYJ3Y9iav1gPJOFPUwC5CibAzF0xuBtqX7xZEySd4rNsLFNF5XG3Z2e8zahqnNibZ0W371AQlYrq6GiaRe0qSbqk/640?wx_fmt=png&from=appmsg "")  
### 2.4. 面向CVE挖掘的专用Finding Agent  
  
**Finding Agent**  
是AutoCVE的核心审计能力  
[citation:1]  
。它专为CVE挖掘设计，能够直接分析项目源码，并结合**ReAct Loop（推理-行动循环）**  
、**专项工具调用**  
以及**结构化终止机制**  
，最终产出符合CVE申报条件的高价值漏洞  
[citation:1]  
。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/opKHaHXLxcku4aCdl3EqU9icYJjVHtNMf9SeGrt9Ne52L57UgbghDTZ2D8rHnd1D2ibp9KNMxmjhlINXEAp1qpKmdI2NEXL1WpcD25gAk3kuM/640?wx_fmt=png&from=appmsg "")  
### 2.5. 交互式审计与全流程追踪  
  
AutoCVE支持用户与审计过程进行交互  
[citation:1]  
。用户可以在审计过程中或审计完成后，围绕结果继续追问，让Agent补充证据、解释攻击链或完善复现步骤。同时，平台集中展示了**活动日志、Agent Tree、工具调用**  
等可视化信息，方便研究者复盘每次审计的执行路径  
[citation:1]  
。  
### 2.6. 智能漏洞管理与Skills扩展  
  
审计发现的漏洞会由Agent自动提交，经过去重后以结构化形式入库，方便统一维护  
[citation:1]  
。此外，平台支持为不同Agent配置专属**Skills**  
，允许用户根据实际需求灵活扩展各Agent的能力边界  
[citation:1]  
。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/opKHaHXLxcnAmPza7Wf5icyedSB6jGtqtRIocKBQVvniaJSwXIxb8owbxFoticLBTkia4NWicdksCVg4hLEPOS0Bx48TmyL2IHomMtJ8C8t6QejA/640?wx_fmt=png&from=appmsg "")  
  
三、立即获取  
  
https://github.com/larlarua/AutoCVE  
  
