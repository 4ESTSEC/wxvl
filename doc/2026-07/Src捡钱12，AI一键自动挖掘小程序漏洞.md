#  Src捡钱12，AI一键自动挖掘小程序漏洞  
an7ln
                    an7ln  山河学安全   2026-07-14 05:09  
  
**免责声明**  
  
本公众号山河学安全旨在分享网络安全领域的相关知识和工具，仅限于学习和研究之用。由于传播、利用本公众号山河学安全所提供的信息而造成的任何直接或者间接的后果及损失，由使用者承担全部法律及连带责任，公众号山河学安全及作者不为此承担任何责任。如有侵权烦请告知，我们会立即删除并致歉，谢谢。**工具安全性自测！**  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/2BCHXx7iavkTeD7TNYmuA54EfmkQzaFJtx9YChJGYjb7v6xQZkNicV0G7a53zQGJVRZUnE0BnExCiat37mg5YxBiafZcPnR2g6ENW6As5vpXSXM/640?from=appmsg "")  
  
**PART**  
  
  
**01**  
  
**工具概述**  
  
本项目是一个本地 Streamable HTTP MCP Server，用于把 WMPFDebugger 暴露的裸 Chrome DevTools Protocol WebSocket 包装成 MCP 工具，辅助 Codex / Cursor / VS Code Agent 对已授权调试的微信小程序进行安全评估、证据采集和报告整理。  
  
**PART**  
  
  
**02**  
  
**使用方法**  
  
**使用边界**  
  
• 仅用于已获得授权的小程序、本机调试环境和当前 WMPFDebugger 会话。  
  
• MCP HTTP 服务只监听 127.0.0.1。  
  
• 默认只连接 ws://127.0.0.1:62000，CDP URL 只允许 127.0.0.1 或 localhost。  
  
• /mcp 必须携带启动时输出的 token。  
  
• 主动请求修改/重放能力未实现；当前工具只做被动采集、单次页面交互观察和人工验证计划生成。  
  
• 点击类工具会拦截支付、提交订单、删除、注销、退款、提现、确认支付等危险文本，默认不执行，必须显式 requireConfirm=true。  
  
**安装与启动**  
  
npm install  
  
npm run dev  
  
也可以指定固定 token：  
  
$env:MCP_TOKEN="your-local-token"  
  
npm run dev  
  
启动后访问根路径查看当前 MCP URL：  
  
http://127.0.0.1:43827/  
  
默认 MCP URL：  
  
http://127.0.0.1:43827/mcp?token=wmpf-local-token  
  
**Codex 配置**  
  
[mcp_servers.wmpf]  
  
enabled = true  
  
url = "http://127.0.0.1:43827/mcp?token=wmpf-local-token"  
  
startup_timeout_sec = 20  
  
tool_timeout_sec = 60  
  
**建议测试提示词**  
  
使用 wmpf MCP，先调用 status，然后 connect_wmpf 连接 ws://127.0.0.1:62000。  
  
随后调用 hook_wx_request 和 hook_fetch_and_xhr，打开当前小程序页面并操作关键业务流程。  
  
再调用 dump_runtime_snapshot、get_all_requests、get_api_inventory、analyze_auth_surface、find_idor_candidates、find_sensitive_data_exposure、find_upload_surfaces、find_payment_and_order_surfaces、find_sign_related_requests。  
  
请基于证据生成 generate_security_notes，只输出发现线索和人工验证建议，不直接下漏洞结论。  
  
**工具列表**  
  
**基础连接与 CDP**  
  
• status：查看 MCP 和 CDP 连接状态。  
  
• connect_wmpf：连接本机 WMPFDebugger CDP WebSocket，并启用 Runtime/Network。  
  
• select_appservice_context：自动枚举 Target、flatten attach，并选择包含 wx.request / require / getCurrentPages 的 appservice Runtime context。  
  
• cdp_call：调用任意 CDP 方法，支持传入 flatten sessionId。  
  
• cdp_call_target：对指定 targetId 自动 Target.attachToTarget({ flatten: true }) 后在子 session 中调用 CDP。  
  
• runtime_eval：在当前 Runtime 执行 JS。  
  
• runtime_eval_appservice：在自动选择的 appservice Runtime context 中执行 JS。  
  
• network_enable：启用 CDP Network。  
  
• get_recent_requests：读取最近 CDP Network 请求，支持 domain、pathPrefix、keyword、excludeStatic、compact 过滤。  
  
• get_response_body：尝试读取 CDP 响应体。  
  
• get_recent_console：读取 console 和 exception 事件。  
  
**运行时与页面探索**  
  
• dump_runtime_snapshot：采集页面运行时快照、storage 摘要、可见文本、可交互元素、疑似框架对象。  
  
• get_basic_page_info：兼容旧版页面信息工具。  
  
• get_document_html：读取 document HTML 前缀。  
  
• query_selector_text：读取指定元素文本和 HTML。  
  
• list_interactive_elements：列出按钮、输入框、链接及高风险关键词元素。  
  
• safe_click_and_observe：单次点击并观察 URL、文本、storage、请求和 console 变化。  
  
• input_text_and_observe：输入文本并观察变化。  
  
• inspect_window_keys：检索 window keys。  
  
• search_global_string：搜索页面、脚本、window keys 中的单个关键词。  
  
**网络采集与接口资产**  
  
• hook_wx_request：自动选择 appservice context 后注入非破坏性 wx.request hook。  
  
• hook_fetch_and_xhr：注入非破坏性 fetch/XHR hook，记录响应和调用栈。  
  
• get_hooked_requests：读取 fetch/XHR hook 记录。  
  
• get_all_requests：汇总 CDP、wx.request、fetch/XHR 请求，并统一格式。  
  
• get_request_detail：查询单个请求详情，CDP 请求会尝试读取响应体。  
  
• get_api_inventory：按 method + path 生成接口资产清单。  
  
**漏洞线索识别**  
  
• analyze_auth_surface：分析认证字段、token 位置、query token、缺失认证和重放线索。  
  
• find_idor_candidates：查找越权候选接口，只给人工验证建议。  
  
• find_sensitive_data_exposure：查找敏感字段并默认脱敏。  
  
• find_upload_surfaces：识别上传、文件、图片、头像、媒体接口。  
  
• find_payment_and_order_surfaces：识别支付、订单、优惠券、钱包、积分接口。  
  
• find_debug_admin_surfaces：识别 debug/test/admin/internal/dev/staging/mock 线索。  
  
• find_sign_related_requests：识别 sign/signature/timestamp/nonce 请求。  
  
**主动验证辅助但默认安全**  
  
• build_replay_plan：根据请求生成 Burp Repeater 人工重放计划，不发送请求。  
  
• compare_two_requests：对比两个请求的 URL、header、body、auth 和 response 差异。  
  
• passive_param_fuzz_suggestions：生成参数 fuzz 建议，不发送请求，并标注需要授权环境人工验证。  
  
**源码与签名逻辑分析**  
  
• inspect_wx_config：读取 __wxConfig、__wxAppCode__、__wxRoute、__wxAppData__ 摘要和关键词命中。  
  
• search_runtime_keywords：搜索 window keys、document HTML、script 文本、storage 和已抓请求。  
  
• trace_request_callstack：从 hook 记录中提取发起请求的 JS 调用栈。  
  
**状态篡改/复原辅助**  
  
• inspect_vuex_store：在 appservice context 中查找 Vuex-like store，保存原始 state 快照，并返回 state/mutations/actions 摘要。  
  
• patch_vuex_state：按 path 修改 state 或调用 mutation。默认 dryRun=true，只有 dryRun=false 且 requireConfirm=true 才会修改本地 Runtime 状态。  
  
• restore_vuex_state：从 inspect_vuex_store 保存的快照恢复 state。默认 dryRun=true，只有 dryRun=false 且 requireConfirm=true 才会恢复。  
  
**报告与证据**  
  
• export_session：导出当前会话 JSON 到 reports/session-*.json。  
  
• generate_security_notes：生成 Markdown 安全评估笔记，只写发现线索和验证建议。  
  
• generate_api_table_markdown：生成接口清单 Markdown 表格。  
  
**推荐工作流**  
  
1. 启动 WMPFDebugger 并确认 DevTools 可连接 ws=127.0.0.1:62000。  
  
2. 启动本项目并把启动输出的 MCP URL 配入 Codex。  
  
3. 调用 connect_wmpf。  
  
4. 调用 select_appservice_context，确认选中的 context 包含 wx.request / require。  
  
5. 调用 hook_wx_request 和 hook_fetch_and_xhr。  
  
6. 在小程序中人工操作登录、搜索、下单前流程、个人中心、地址、优惠券、上传等页面。  
  
7. 使用 get_recent_requests 的 excludeStatic=true、domain、pathPrefix 过滤图片、字体、data URI 等噪声。  
  
8. 调用 get_api_inventory 和各类 find_* 工具生成线索。  
  
9. 对候选接口使用 build_replay_plan、compare_two_requests、passive_param_fuzz_suggestions 制定人工验证步骤。  
  
10. 如需验证前端状态授权绕过，先调用 inspect_vuex_store 保存快照，再用 patch_vuex_state dry-run 预览；确认后才设置 dryRun=false 和 requireConfirm=true。结束后用 restore_vuex_state 复原。  
  
11. 调用 export_session 和 generate_security_notes 保存证据与笔记。  
  
**构建**  
  
npm run build  
  
npm start  
  
**PART**  
  
  
**03**  
  
**工具下载**  
  
**关注名片进入公众号**  
  
**回复关键字【260714】获取下载链接**  
  
