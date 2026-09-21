#  SRC漏洞报告成稿 Skill（SRC/0day 提交稿、分层验证门、Step 式 PoC、截图铁律）  
v-yun
                    v-yun  HACK之道   2026-09-21 00:53  
  
   
  
### 介绍  
  
  
### 一个 Claude Code skill：把已确认的漏洞写成可直接提交 SRC / 0day 平台审核方的 DOCX 提交稿。  
  
  
### 工作流程 / Workflow  
  
  
   
  
![](https://mmbiz.qpic.cn/mmbiz_png/HqolA1dQic6ibicU8RCWbfeqHJyria1aaU8VeSdv5hSrzkTbyvEdjIqAibot5J3jDlRCGYhvBERUnVjqfQBeGrZFDkG4EGZ9J9qiagrOglRXovVWY/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/HqolA1dQic69xOZaL0IydlUNXhY8S8CrOiar2vpOWibkRcwPWia7F3lwKGicoPQMamE3T9IQ51sQw0Ubdbl7XopKgoeg8SfurcMibPnAbYHFWEYU4/640?wx_fmt=png&from=appmsg "")  
  
   
  
### 它约束什么  
  
  
   
- **双重审查机制**  
：第一道在**挖掘过程中**  
跑——遇到信号先快筛（明显不够格的当场止损，不浪费 token 深挖）；第二道在成稿前跑——完整分层验证门（硬门 / 按类型命门表 / 0day 收录审查门）。不过门不成稿，从源头挡掉"信号当漏洞"的垃圾报告。  
  
- **双重可读写作标准**  
：产品经理照着 Step 能复现，安全工程师看完觉得技术扎实——两个维度缺一返工。  
  
- **固定 DOCX 版式**  
：template.docx  
 内置全部样式（微软雅黑、Heading 2 章节、全文统一黑色），python-docx 逐节生成，朴素线性文档，不堆表格卡片。  
  
- **Step 式 PoC 规格**  
：每步 = 一句话标题 + 操作上下文 + 原始请求块（从 Burp/Yakit 等任意抓包工具原文复制，不放 curl）+ 结果结论 + 真实截图。  
  
- **截图铁律**  
：每步必须配真实目标截图，严禁自造渲染。截图**全程后台完成**  
（headless/CDP），不把浏览器或抓包工具窗口弹到前台；数据包以原始文本块入报告，截工具界面时只截请求/响应详情面板，不截历史列表。  
  
- **简洁硬规 + 去 AI 腔硬规**  
：消灭八股标签、填充语、形容词渲染、模板化句式——平台 AI 检测和人工直觉都会筛掉模板腔报告。  
  
- **完整成稿流程**  
：查重 → 过验证门 → 生成 DOCX → 语义化命名 → 归档 → 更新/驳回两种处理 → 收尾清理。  
  
   
  
### 适用场景  
  
  
   
- 企业 SRC 漏洞提交稿（各厂商 SRC 通用版式）  
  
- EDUSRC 教育行业漏洞报告  
  
- 0day / 通用产品漏洞报告（内置通用型模板章节骨架 + 收录审查门）  
  
项目地址  
  
https://github.com/v-yun/vuln-report-skill/  
  
  
