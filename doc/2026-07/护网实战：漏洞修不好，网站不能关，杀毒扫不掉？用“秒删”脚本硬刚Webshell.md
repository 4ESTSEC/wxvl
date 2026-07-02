#  护网实战：漏洞修不好，网站不能关，杀毒扫不掉？用“秒删”脚本硬刚Webshell  
原创 流量名侦探
                    流量名侦探  流量名侦探   2026-07-01 23:40  
  
## 前言：护网人最绝望的三重困境  
  
护网行动中，如果你突然收到告警：**服务器被上传了Webshell**  
。  
  
紧接着你面临的是三座大山：  
1. **漏洞无法修复**  
——开发说要排期，代码不敢动  
  
1. **网站不能停摆**  
——业务一分一秒都断不得  
  
1. **杀毒软件扫不掉**  
——免杀Webshell过掉了所有引擎，或者文件藏得太深  
  
眼看着攻击者可能随时执行命令、拖库、横向扩散，你能做什么？  
> **答案是：让他传，但让他传了个寂寞。**  
  
  
今天这篇文章，分享一个护网实战中屡试不爽的“歪招”——**秒删脚本**  
。既然我修不好漏洞，那我就让你的Webshell**落地即消失**  
，连URL都来不及敲，文件就没了。  
## 一、为什么“秒删”比杀毒软件更管用？  
<table><thead><tr><th data-colwidth="112" style="border-bottom: 0.909091px solid rgba(0, 0, 0, 0.12);font: 500 15px / 25px quote-cjk-patch, Inter, system-ui, -apple-system, BlinkMacSystemFont, &#34;Segoe UI&#34;, Roboto, Oxygen, Ubuntu, Cantarell, &#34;Open Sans&#34;, &#34;Helvetica Neue&#34;, sans-serif;border-top-width: medium;border-top-style: none;border-top-color: currentcolor;min-width: 100px;max-width: min(30vw, 320px);padding: 10px 16px 10px 0px;text-align: left;"><section><span leaf="">      方案</span></section></th><th data-colwidth="162" style="border-bottom: 0.909091px solid rgba(0, 0, 0, 0.12);font: 500 15px / 25px quote-cjk-patch, Inter, system-ui, -apple-system, BlinkMacSystemFont, &#34;Segoe UI&#34;, Roboto, Oxygen, Ubuntu, Cantarell, &#34;Open Sans&#34;, &#34;Helvetica Neue&#34;, sans-serif;border-top-width: medium;border-top-style: none;border-top-color: currentcolor;min-width: 100px;max-width: min(30vw, 320px);padding: 10px 16px;text-align: left;"><section><span leaf="">       原理</span></section></th><th data-colwidth="307" style="border-bottom: 0.909091px solid rgba(0, 0, 0, 0.12);font: 500 15px / 25px quote-cjk-patch, Inter, system-ui, -apple-system, BlinkMacSystemFont, &#34;Segoe UI&#34;, Roboto, Oxygen, Ubuntu, Cantarell, &#34;Open Sans&#34;, &#34;Helvetica Neue&#34;, sans-serif;border-top-width: medium;border-top-style: none;border-top-color: currentcolor;min-width: 100px;max-width: min(30vw, 320px);padding: 10px 16px;text-align: left;"><section><span leaf="">                 局限性</span></section></th></tr></thead><tbody><tr><td data-colwidth="112" style="border-bottom: 0.909091px solid rgba(0, 0, 0, 0.1);font: 400 15px / 25px quote-cjk-patch, Inter, system-ui, -apple-system, BlinkMacSystemFont, &#34;Segoe UI&#34;, Roboto, Oxygen, Ubuntu, Cantarell, &#34;Open Sans&#34;, &#34;Helvetica Neue&#34;, sans-serif;min-width: 100px;max-width: min(30vw, 320px);padding: 10px 16px 10px 0px;"><strong style="font-weight: 600;"><span leaf="">   杀毒软件</span></strong></td><td data-colwidth="162" style="border-bottom: 0.909091px solid rgba(0, 0, 0, 0.1);font: 400 15px / 25px quote-cjk-patch, Inter, system-ui, -apple-system, BlinkMacSystemFont, &#34;Segoe UI&#34;, Roboto, Oxygen, Ubuntu, Cantarell, &#34;Open Sans&#34;, &#34;Helvetica Neue&#34;, sans-serif;min-width: 100px;max-width: min(30vw, 320px);padding: 10px 16px;"><section><span leaf=""> 特征库匹配</span></section></td><td data-colwidth="307" style="border-bottom: 0.909091px solid rgba(0, 0, 0, 0.1);font: 400 15px / 25px quote-cjk-patch, Inter, system-ui, -apple-system, BlinkMacSystemFont, &#34;Segoe UI&#34;, Roboto, Oxygen, Ubuntu, Cantarell, &#34;Open Sans&#34;, &#34;Helvetica Neue&#34;, sans-serif;min-width: 100px;max-width: min(30vw, 320px);padding: 10px 0px 10px 16px;"><section><span leaf="">免杀Webshell分分钟绕过，0day根本识别不了</span></section></td></tr><tr><td data-colwidth="112" style="border-bottom: 0.909091px solid rgba(0, 0, 0, 0.1);font: 400 15px / 25px quote-cjk-patch, Inter, system-ui, -apple-system, BlinkMacSystemFont, &#34;Segoe UI&#34;, Roboto, Oxygen, Ubuntu, Cantarell, &#34;Open Sans&#34;, &#34;Helvetica Neue&#34;, sans-serif;min-width: 100px;max-width: min(30vw, 320px);padding: 10px 16px 10px 0px;"><strong style="font-weight: 600;"><span leaf="">   WAF拦截</span></strong></td><td data-colwidth="162" style="border-bottom: 0.909091px solid rgba(0, 0, 0, 0.1);font: 400 15px / 25px quote-cjk-patch, Inter, system-ui, -apple-system, BlinkMacSystemFont, &#34;Segoe UI&#34;, Roboto, Oxygen, Ubuntu, Cantarell, &#34;Open Sans&#34;, &#34;Helvetica Neue&#34;, sans-serif;min-width: 100px;max-width: min(30vw, 320px);padding: 10px 16px;"><section><span leaf=""> 流量特征检测</span></section></td><td data-colwidth="307" style="border-bottom: 0.909091px solid rgba(0, 0, 0, 0.1);font: 400 15px / 25px quote-cjk-patch, Inter, system-ui, -apple-system, BlinkMacSystemFont, &#34;Segoe UI&#34;, Roboto, Oxygen, Ubuntu, Cantarell, &#34;Open Sans&#34;, &#34;Helvetica Neue&#34;, sans-serif;min-width: 100px;max-width: min(30vw, 320px);padding: 10px 0px 10px 16px;"><section><span leaf="">加密流量（冰蝎、哥斯拉）根本看不懂</span></section></td></tr><tr><td data-colwidth="112" style="border-bottom: 0.909091px solid rgba(0, 0, 0, 0.1);font: 400 15px / 25px quote-cjk-patch, Inter, system-ui, -apple-system, BlinkMacSystemFont, &#34;Segoe UI&#34;, Roboto, Oxygen, Ubuntu, Cantarell, &#34;Open Sans&#34;, &#34;Helvetica Neue&#34;, sans-serif;min-width: 100px;max-width: min(30vw, 320px);padding: 10px 16px 10px 0px;"><strong style="font-weight: 600;"><span leaf="">    定时查杀</span></strong></td><td data-colwidth="162" style="border-bottom: 0.909091px solid rgba(0, 0, 0, 0.1);font: 400 15px / 25px quote-cjk-patch, Inter, system-ui, -apple-system, BlinkMacSystemFont, &#34;Segoe UI&#34;, Roboto, Oxygen, Ubuntu, Cantarell, &#34;Open Sans&#34;, &#34;Helvetica Neue&#34;, sans-serif;min-width: 100px;max-width: min(30vw, 320px);padding: 10px 16px;"><section><span leaf=""> 定时扫描目录</span></section></td><td data-colwidth="307" style="border-bottom: 0.909091px solid rgba(0, 0, 0, 0.1);font: 400 15px / 25px quote-cjk-patch, Inter, system-ui, -apple-system, BlinkMacSystemFont, &#34;Segoe UI&#34;, Roboto, Oxygen, Ubuntu, Cantarell, &#34;Open Sans&#34;, &#34;Helvetica Neue&#34;, sans-serif;min-width: 100px;max-width: min(30vw, 320px);padding: 10px 0px 10px 16px;"><section><span leaf="">攻击者几秒内就执行完了命令，你扫到已经晚了</span></section></td></tr><tr><td data-colwidth="112" style="border-bottom: 0.909091px solid rgba(0, 0, 0, 0.1);font: 400 15px / 25px quote-cjk-patch, Inter, system-ui, -apple-system, BlinkMacSystemFont, &#34;Segoe UI&#34;, Roboto, Oxygen, Ubuntu, Cantarell, &#34;Open Sans&#34;, &#34;Helvetica Neue&#34;, sans-serif;min-width: 100px;max-width: min(30vw, 320px);padding: 10px 16px 10px 0px;"><strong style="font-weight: 600;"><span leaf="">    秒删脚本</span></strong></td><td data-colwidth="162" style="border-bottom: 0.909091px solid rgba(0, 0, 0, 0.1);font: 400 15px / 25px quote-cjk-patch, Inter, system-ui, -apple-system, BlinkMacSystemFont, &#34;Segoe UI&#34;, Roboto, Oxygen, Ubuntu, Cantarell, &#34;Open Sans&#34;, &#34;Helvetica Neue&#34;, sans-serif;min-width: 100px;max-width: min(30vw, 320px);padding: 10px 16px;"><section><span leaf="">文件落地瞬间删除</span></section></td><td data-colwidth="307" style="border-bottom: 0.909091px solid rgba(0, 0, 0, 0.1);font: 400 15px / 25px quote-cjk-patch, Inter, system-ui, -apple-system, BlinkMacSystemFont, &#34;Segoe UI&#34;, Roboto, Oxygen, Ubuntu, Cantarell, &#34;Open Sans&#34;, &#34;Helvetica Neue&#34;, sans-serif;min-width: 100px;max-width: min(30vw, 320px);padding: 10px 0px 10px 16px;"><strong style="font-weight: 600;"><span leaf="">不依赖特征库，不依赖规则，纯物理删除</span></strong></td></tr></tbody></table>  
秒删的核心优势在于：**不管你是免杀马还是0day马，只要后缀是 .jsp/.php/.asp，落地就删。**  
 简单粗暴，但极其有效。  
## 二、适用场景（真实护网案例）  
- 老系统存在文件上传漏洞，但代码没人敢动  
  
- 业务7x24小时不能中断，没法停机修复  
  
- 查杀工具扫不出Webshell（冰蝎/哥斯拉内存马或免杀变种）  
  
- 攻击者自动化批量上传，人工根本来不及删  
  
- 护网期间溯源需要保留样本，但又不能让文件存活  
  
遇到以上任意一条，“秒删”就是你最后的防线。  
## 三、实战方案一：Linux系统   
### 第一步：创建秒删脚本  
  
```
cat > /root/kill_webshell_loop.sh << 'EOF'
#!/bin/bash

WATCH_DIR="/var/www/html/uploads"
LOG_FILE="/var/log/ultra_fast_delete.log"

echo "========================================" >> "$LOG_FILE"
echo "[$(date '+%Y-%m-%d %H:%M:%S')] 循环秒删监控已启动（无inotify版本）" >> "$LOG_FILE"

while true; do
    find "$WATCH_DIR" -type f \( \
        -name "*.jsp" -o -name "*.jspx" -o \
        -name "*.php" -o -name "*.php[0-9]" -o -name "*.phtml" -o \
        -name "*.asp" -o -name "*.aspx" -o -name "*.ashx" -o -name "*.asmx" -o \
        -name "*.cer" -o -name "*.cdx" -o -name "*.asa" \
    \) 2>/dev/null | while read FILE; do
        rm -f "$FILE" 2>/dev/null
        echo "[$(date '+%Y-%m-%d %H:%M:%S.%3N')] DELETED: $FILE" >> "$LOG_FILE"
    done
    sleep 0.5   # 每0.5秒扫描一次
done
EOF

chmod +x /root/kill_webshell_loop.sh
```  
  
### 第二步：后台运行  
```
nohup /root/kill_webshell_loop.sh >> /var/log/ultra_fast_delete_nohup.log 2>&1 &
```  
### 第三步：验证测试  
```
touch /var/www/html/uploads/shell.jsp
ls -la /var/www/html/uploads/shell.jsp
# 输出：No such file or directory —— 秒删成功！
```  
## 四、实战方案二：Windows系统  
### 第一步：创建秒删脚本  
```
# ============================================
# 文件名: C:\kill_webshell_fast.ps1
# 功能: 极速秒删（带微秒级重试，无备份阻塞）
# 运行: powershell -ExecutionPolicy Bypass -File C:\kill_webshell_fast.ps1
# ============================================

# ---------- 配置 ----------
$watch_dir = "D:\webapps\upload"
$log_file = "C:\ultra_fast_delete.log"

# 清空旧日志（或保留追加）
"========================================" | Out-File $log_file -Append
"[$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss.fff')] 极速监控已启动" | Out-File $log_file -Append

# ---------- 创建监控器 ----------
$watcher = New-Object System.IO.FileSystemWatcher
$watcher.Path = $watch_dir
$watcher.IncludeSubdirectories = $true
$watcher.EnableRaisingEvents = $true

# ---------- 定义删除动作（无备份，纯删除） ----------
$action = {
    $path = $Event.SourceEventArgs.FullPath
    $ext = [System.IO.Path]::GetExtension($path)

    # 匹配高危后缀
    if ($ext -match '\.(jsp|php|asp|aspx|jspx|php5|phtml|cer|cdx|asa|ashx|asmx)$') {
        $deleted = $false

        # ===== 核心：毫秒级重试循环（尝试20次，每次等待10ms） =====
        for ($i = 0; $i -lt 20; $i++) {
            try {
                # 用 .NET 原生方法直接删除，比 Remove-Item 快数倍
                [System.IO.File]::Delete($path)
                $deleted = $true
                break
            } catch {
                # 文件被占用（正在上传或扫描），等待10ms后重试
                Start-Sleep -Milliseconds 10
            }
        }

        # ---------- 记录结果（不影响删除速度） ----------
        $time = Get-Date -Format "HH:mm:ss.fff"
        if ($deleted) {
            $msg = "[$time] 秒删成功: $path"
        } else {
            $msg = "[$time] 删除失败(可能已移走): $path"
        }
        # 输出到控制台（方便前台调试）
        Write-Host $msg
        # 写入日志文件
        Add-Content -Path $log_file -Value $msg
    }
}

# ---------- 绑定事件（两种方式都绑，覆盖创建和修改） ----------
Register-ObjectEvent $watcher "Created" -Action $action -SourceIdentifier "FastKill"
Register-ObjectEvent $watcher "Changed" -Action $action -SourceIdentifier "FastKillChange"

Write-Host "========================================" -ForegroundColor Green
Write-Host "极速秒删已就绪！" -ForegroundColor Green
Write-Host "监控目录: $watch_dir" -ForegroundColor Yellow
Write-Host "按 Ctrl+C 退出" -ForegroundColor Gray
Write-Host "========================================" -ForegroundColor Green

# 保持脚本在前台运行（Ctrl+C 可退出）
while ($true) { Start-Sleep -Seconds 1 }
```  
### 第二步：后台运行（管理员权限）  
```
 powershell -ExecutionPolicy Bypass -File "C:\kill_webshell.ps1"
```  
## 五、护网特别提醒：不要删了就完了！  
### 1. 配置开机自启  
  
确保服务器重启后秒删脚本自动运行：  
- **Linux**  
：配置systemd服务  
  
- **Windows**  
：用NSSM注册为服务  
  
### 2. 配合“目录禁止执行”使用（双保险）  
  
秒删存在微小的“时间窗口”，最稳妥的做法是**同时禁止上传目录执行脚本**  
：  
- **Nginx**  
：location ~* /uploads/.*\.(php|jsp)$ { return 403; }  
  
- **IIS**  
：取消上传目录的“脚本”执行权限  
  
双重保险，让攻击者彻底绝望。  
## 结语  
  
护网是一场攻防不对等的对抗。攻击者可以24小时自动化扫描、上传、利用，而防守方往往受限于业务稳定、变更流程、漏洞修复周期，有力使不出。  
  
在这种困境下，**“秒删”不是最优解，但它是你能在5分钟内落地的最强止血方案。**  
 它不依赖漏洞修复、不依赖杀毒特征库、不依赖业务停机，纯粹从物理层面阻止Webshell存活。  
> **你传任你传，我删不留痕。**  
  
  
希望这套方案能在护网实战中帮到你。  
  
  
  
  
  
  
  
  
  
  
  
