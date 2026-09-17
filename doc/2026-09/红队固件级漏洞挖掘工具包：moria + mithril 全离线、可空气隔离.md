#  红队固件级漏洞挖掘工具包：moria + mithril 全离线、可空气隔离  
原创 Red Hunter
                    Red Hunter  黑白之道   2026-09-17 00:35  
  
地址![](https://mmbiz.qpic.cn/sz_mmbiz_png/nGzNudUIJ6NLyxiaiaicK3mNGvTps5s1W4LzWhkSFtqBDK7YUA8k8qlicrBCgysVZt8M0jOI7YZ2gVJu9lcZ4ictAmJ9QPKxgB5DcTHUHJCR14jw/640?from=appmsg "")  
> **导语**  
：安全研究员 Matt Brown（@nmatt0）本周放出两款 MIT 协议的开源 IoT 固件分析工具——moria 负责把固件拆成零件，mithril 负责从零件里挖出秘银。整套组合拳让你不用碰物理设备，就能完成固件级漏洞挖掘、SBOM 生成、密钥泄露检测、启动安全审计。命名致敬《魔戒》"摩瑞亚矿坑里的秘银"。  
  
## 一、背景：IoT 固件分析的痛点  
  
红队做 IoT 渗透的传统路线是：拿到目标设备 → 拆机 → 找调试口 → 抓固件 → 用 binwalk 拆包。这一步卡在"拿到设备"上——甲方不肯借、厂商不发样品、设备压根买不到都是常事。  
  
moria 和 mithril 的价值就是把"拆机"前置到下载阶段。从厂商官网下载公开固件升级包，或从 OTA 服务器抓一个 .bin 文件，剩下的活就交给这两个工具搞定。作者 Matt Brown 把它们定位成"在你拿到设备之前，先把设备的内裤扒光"——糙理不糙。  
  
![IoT 固件挖掘双剑封面图](https://mmbiz.qpic.cn/mmbiz_png/nGzNudUIJ6Of0nRFzW8u2YFd1jGrs0MRjukAwAVnggQ0jasDhCUGG1aaQuxFG2D0ib0NCuxXGcibPcm4hSUibEIEdlmbKQEs5XR8E4evySb0cE/640?from=appmsg "IoT 固件挖掘双剑封面图")  
  
作者用了《魔戒》命名：moria 是摩瑞亚矿坑，mithril 是矿坑里的秘银。moria 钻进固件二进制海洋，mithril 把藏在里面的秘银（漏洞、密钥、组件信息）挖出来。README 里直接写明："moria maps the bytes. mithril reads the contents."  
## 二、moria：固件"拆箱"利器  
  
moria 是 C++20 写的命令行工具：在不 root、不装外部工具的前提下，对固件进行结构识别、嵌套解压、字节雕刻。  
  
支持的文件系统覆盖 IoT 固件所有主流格式：SquashFS、ext2/3/4、F2FS、XFS、btrfs、HFS+、NTFS、EROFS、JFFS2、UBIFS 等十余种，还有 RAE Systems / Honeywell RFP 这种工业厂商专有的固件容器格式。归档覆盖 ZIP、tar、cpio、ISO 9660、Android sparse；启动包装层支持 U-Boot uImage、U-Boot FIT、gzip/xz/zstd/lz4 裸流。  
  
几个贴实战场景的特性：**递归是默认行为**  
——gzip 包着的 SquashFS 再嵌进 UBI 卷，一路拆到底；**UPX 加壳识别**  
——检测 ELF/PE/Mach-O 是否被 UPX 压缩，连"为了对抗 upx -d 把头部清零"的反脱壳手段都能识别；**抗恶意输入**  
——所有读操作做边界检查，所有写操作走 openat + O_NOFOLLOW，解压做了压缩炸弹防护。  
  
输出默认是给人看的层级树，加 -j  
 切 JSON，方便脚本和大模型 agent 接管。这是 moria 跟 binwalk 最大的差异化——后者输出基本是给人看的，前者天然适配 AI 工作流。  
## 三、mithril：固件"读心"专家  
  
mithril 是 moria 的语义层搭档：moria 拆完，mithril 接手做内容分析。同样 C++20 写，无第三方依赖。  
  
能力按"维度"展开。**SBOM 生成**  
——除了 dpkg/opkg/apk/rpm 这种包管理器数据库，还能从 ELF 版本字符串、libc 文件名、内核 banner 里恢复组件信息。输出格式 CycloneDX 和 SPDX。  
  
![moria + mithril 双阶段工作流](https://mmbiz.qpic.cn/sz_mmbiz_png/nGzNudUIJ6NQlFicgd1pcaqiadxbURDYMnf4NStP3R9cwEN9TPsdP40PaXNv0BRe93jsNnTkkzUc4MSz1QgXUydiaAXjsCeNNm628Oe3UKFgpY/640?from=appmsg "moria + mithril 双阶段工作流")  
  
**CVE 匹配**  
——本地维护一份 OSV + NVD/CPE 镜像，加一份手工筛选的"高信号内核 CVE 清单"。匹配结果用 CISA KEV 和 EPSS 打标签。最关键的是：完全离线、可空气隔离，涉密环境也能跑。  
  
**密钥泄露检测**  
是一大亮点。mithril 用"确定性离线阶梯"：先看模式（密钥形状），再验证结构（能解析成 JWT 或 PEM 私钥），最后做数学校验（GitHub token 的校验和、加密哈希）。它永远只说"看起来有效"，绝不去打活靶——只判断"如果攻击者拿到这条字符串，他能不能用"。  
  
更狠的是**弱公钥与泄露公钥检测**  
：mithril 把固件里所有公钥拿来跟一份内置的"私钥已公开"语料库比对——rapid7 ssh-badkeys、Vagrant 不安全密钥全在；自动识别 ROCA 指纹（CVE-2017-15361）；用 Fermat 临近素数、batch-GCD 共享素数、Wiener 小私钥指数检测来找能直接分解 RSA 模数的漏洞。命中任一个，就拿到了伪造签名或绕过 SSH 鉴权的能力。  
  
**启动安全审计**  
——--boot  
 pass 读 U-Boot 环境变量、设备树、FIT/AVB/UEFI 验证启动配置；可直接拿一个裸的 AMI/EDK2 BIOS 文件做 UEFI Secure Boot 姿势分析。挖"启动链信任根"类漏洞时必跑。  
## 四、组合拳：从固件到漏洞清单  
  
完整工作流就是两条命令加一次数据库初始化：  
```
moria -e firmware.bin                     # 拆箱：识别 + 嵌套解压mithril --fetch-db                        # 首次：拉一份本地 CVE 镜像（仅此一次联网）mithril firmware.bin.extracted/           # 读心：密钥 + SBOM + CVE + 授权 + 启动 + 公钥
```  
  
加几个常用 flag 让结果更聚焦：--secrets  
（只看密钥）、--sbom -C out/  
（导出 SBOM）、--cve --component-cves-all  
（列全组件 CVE）、--keys  
（公钥弱性检测）、--boot  
（启动安全审计）。JSON 输出配合 -j  
 可直接喂给脚本或 LLM agent。  
  
构建也是干干净净：两条 cmake 命令搞定，零运行时依赖，单文件二进制，复制到 ~/.local/bin/  
 就能用。MIT 协议，可商用、可改造、无 copyleft 污染。  
## 五、红队实战视角与适用场景  
  
作为红队从业者，我看到这套工具最直接的三个用法。  
  
**第一，供应商情报收集**  
。拿到目标厂商的产品列表，从每个型号的官方下载页抓最新固件，跑一遍 moria + mithril，就能拿到 SBOM 和 CVE 命中清单。这是写渗透测试报告"漏洞影响面分析"章节的金矿——很多时候甲方自己都不知道自家设备装着什么版本的 openssl。  
  
**第二，0day 候选筛选**  
。mithril 的 SBOM + CVE 匹配自动标 CISA KEV 和高 EPSS，直接看到"这个组件已被野外利用过"的项。再叠加 --component-cves-all  
 看全量，2024-2026 那些活跃利用的内核提权、busybox 命令注入、uClibc 越界读漏洞都能按组件版本号精准命中。  
  
**第三，加密供应链弱点审计**  
。--keys  
 pass 那一套 ROCA + 弱 RSA + 共享素数检测，是 2017-2018 年 Infineon TPM 漏洞之后极少数能持续用的离线检测能力。直接给客户演示就值回票价。  
  
适用边界要说清楚：moria + mithril 是**静态分析**  
工具，面对加密固件就抓瞎——除非你能搞到解密后的 bootloader 镜像或调试固件。它们输出的是"候选漏洞清单"，不是"已验证漏洞"，最后一步还得红队手动写 PoC 或跑真实设备测试。  
  
作者 Matt Brown 自己说："完全离线、可复现、可空气隔离。" 在涉密网络、隔离研发网、关键基础设施评估场景里，这一条比任何花哨功能都值钱。  
  
  
开源工具地址：  
  
moria: https://github.com/nmatt0/moria  
  
mithril: https://github.com/nmatt0/mithril  
  
**版权声明**  
：本文由华盟网原创发布，保留所有权利。配图由华盟网授权使用。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_jpg/nGzNudUIJ6Nb1v2vPsyicrR0icZ2oUatUribo92MvnicrFumLPdu9MDx1rteibdg0KmBb3ISDau0JCUKdMAq52MUkdaAqAw6RVD0oUAaJ4mQZuFA/640?from=appmsg "")  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650621549&idx=1&sn=21c4b072726d2387d562109ada6b9bbb&scene=21#wechat_redirect)  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650621944&idx=1&sn=3cc6dc9876a20466d4ec3634deb80220&scene=21#wechat_redirect)  
  
[](https://mp.weixin.qq.com/s?__biz=MzAxMjE3ODU3MQ==&mid=2650622065&idx=1&sn=09f8ae84c06d4331e71c277b177ae701&scene=21#wechat_redirect)  
> 👇 点击**阅读原文**  
，访问我的网站  
  
  
  
