#  五域 skill(资产侦察/漏洞扫描/漏洞利用/ 后渗透操作/攻击产物分析)  
raystyle
                    raystyle  网络安全者   2026-09-21 01:56  
  
===================================  
  
**免责声明**  
  
请勿利用文章内的相关技术从事非法测试，由于传播、利用此文所提供的信息而造成的任何直接或者间接的后果及损失，均由使用者本人负责，作者不为此承担任何责任。工具来自网络，安全性自测，如有侵权请联系删除。  
个人微信：ivu123ivu  
  
  
**0x01 工具介绍**  
  
pentest_rs(agent plugin 层):五域 skill(资产侦察/漏洞扫描/漏洞利用/后渗透操作/攻击产物分析)+ rs-scripting 创作技能,教 agent 使用并按需编写沉淀 rust-script 脚本。执行层在 raystyle/prs_workspace(pdt = Pentest Development Toolkit)。仅限合法授权测试。  
  
```
.claude-plugin/ .codex-plugin/    双运行时 manifest
skills/<域>/SKILL.md              五域技能(用法速查+按需编写沉淀+安全边界)
skills/rs-scripting/          跨域创作技能(SKILL.md=创作指南;references/INSTALL.md)
skills/post-exploitation/references/   域专属参考(WQL/CIM 操作手册)
agents/pentest-operator.toml      Codex 操作员子代理
```  
  
**0x02 安装与使用**  
  
常用命令：  
<table><thead><tr style="box-sizing: border-box;background-color: rgb(255, 255, 255);border-top: 0.888889px solid rgba(209, 217, 224, 0.7);"><th style="box-sizing: border-box;padding: 6px 13px;font-weight: 600;border: 0.888889px solid rgb(209, 217, 224);"><section><span leaf="">类别</span></section></th><th style="box-sizing: border-box;padding: 6px 13px;font-weight: 600;border: 0.888889px solid rgb(209, 217, 224);"><section><span leaf="">域(目录)</span></section></th><th style="box-sizing: border-box;padding: 6px 13px;font-weight: 600;border: 0.888889px solid rgb(209, 217, 224);"><section><span leaf="">数量</span></section></th></tr></thead><tbody><tr style="box-sizing: border-box;background-color: rgb(255, 255, 255);border-top: 0.888889px solid rgba(209, 217, 224, 0.7);"><td style="box-sizing: border-box;padding: 6px 13px;border: 0.888889px solid rgb(209, 217, 224);"><section><span leaf="">资产侦察</span></section></td><td style="box-sizing: border-box;padding: 6px 13px;border: 0.888889px solid rgb(209, 217, 224);"><section><span leaf="">asset-reconnaissance/</span></section></td><td style="box-sizing: border-box;padding: 6px 13px;border: 0.888889px solid rgb(209, 217, 224);"><section><span leaf="">7</span></section></td></tr><tr style="box-sizing: border-box;background-color: rgb(246, 248, 250);border-top: 0.888889px solid rgba(209, 217, 224, 0.7);"><td style="box-sizing: border-box;padding: 6px 13px;border: 0.888889px solid rgb(209, 217, 224);"><section><span leaf="">漏洞扫描</span></section></td><td style="box-sizing: border-box;padding: 6px 13px;border: 0.888889px solid rgb(209, 217, 224);"><section><span leaf="">vulnerability-scan/</span></section></td><td style="box-sizing: border-box;padding: 6px 13px;border: 0.888889px solid rgb(209, 217, 224);"><section><span leaf="">10(根级)+ 4042 转译件(见 CATALOG)</span></section></td></tr><tr style="box-sizing: border-box;background-color: rgb(255, 255, 255);border-top: 0.888889px solid rgba(209, 217, 224, 0.7);"><td style="box-sizing: border-box;padding: 6px 13px;border: 0.888889px solid rgb(209, 217, 224);"><section><span leaf="">漏洞利用</span></section></td><td style="box-sizing: border-box;padding: 6px 13px;border: 0.888889px solid rgb(209, 217, 224);"><section><span leaf="">initial-exploitation/</span></section></td><td style="box-sizing: border-box;padding: 6px 13px;border: 0.888889px solid rgb(209, 217, 224);"><section><span leaf="">2</span></section></td></tr><tr style="box-sizing: border-box;background-color: rgb(246, 248, 250);border-top: 0.888889px solid rgba(209, 217, 224, 0.7);"><td style="box-sizing: border-box;padding: 6px 13px;border: 0.888889px solid rgb(209, 217, 224);"><section><span leaf="">后渗透操作</span></section></td><td style="box-sizing: border-box;padding: 6px 13px;border: 0.888889px solid rgb(209, 217, 224);"><section><span leaf="">post-exploitation/</span></section></td><td style="box-sizing: border-box;padding: 6px 13px;border: 0.888889px solid rgb(209, 217, 224);"><section><span leaf="">3(wssd_c2、wmi_client、wssd_persist_service)</span></section></td></tr><tr style="box-sizing: border-box;background-color: rgb(255, 255, 255);border-top: 0.888889px solid rgba(209, 217, 224, 0.7);"><td style="box-sizing: border-box;padding: 6px 13px;border: 0.888889px solid rgb(209, 217, 224);"><section><span leaf="">攻击产物分析</span></section></td><td style="box-sizing: border-box;padding: 6px 13px;border: 0.888889px solid rgb(209, 217, 224);"><section><span leaf="">artifact-analysis/</span></section></td><td style="box-sizing: border-box;padding: 6px 13px;border: 0.888889px solid rgb(209, 217, 224);"><section><span leaf="">1</span></section></td></tr></tbody></table>  
  
一定要在虚拟机运行，工具下载链接：  
  
公众号后台回复：20260921  
  
链接仅一天有效，每日更新  
  
  
  
**·****今 日 推 荐**  
**·**  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/PQNvx9ufMAjrFrLhhbE4sR3EAbepuZuAwN51dSAoqPbmicFGcWu5sQBMicvWnYctUZl9iapvd9icHpNib8VLbSyu338BjUJoDvRGian4XWRZGpxY8/640?wx_fmt=jpeg&from=appmsg "")  
  
  
  
  
  
