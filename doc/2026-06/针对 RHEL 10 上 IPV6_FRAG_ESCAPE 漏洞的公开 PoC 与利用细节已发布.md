#  针对 RHEL 10 上 IPV6_FRAG_ESCAPE 漏洞的公开 PoC 与利用细节已发布  
sec随谈
                    sec随谈  sec随谈   2026-06-30 01:33  
  
**安全研究员 Massimiliano Oldani 发布 IPV6_FRAG_ESCAPE：针对 CentOS 与 RHEL 10 的 IPv6 容器逃逸概念验证**  
  
安全研究员 Massimiliano Oldani 发布了 IPV6_FRAG_ESCAPE——一个针对 CentOS 和 RHEL 10 的 IPv6 容器逃逸(container escape)的可用概念验证(PoC)。其利用细节和 PoC 代码现已公开。该攻击链能将隔离容器内一个非特权进程,提升为宿主机上的 root shell（最高权限命令行）。  
  
**摘要**  
  
IPV6_FRAG_ESCAPE 利用了 Linux 内核中一个现已修复的 IPv6 分片(fragmentation)漏洞。该缺陷位于 __ip6_append_data()  
 函数中,会破坏数据包自身的 skb_shared_info  
 结构。Oldani 公开发布了完整的攻击链,该漏洞未被分配 CVE 编号。  
  
**这个 IPv6 容器逃逸为何重要**  
  
容器隔离是一道核心的安全边界,而这个漏洞打破了它。已经能在容器内执行代码的攻击者,可借此触及宿主机的 root 文件系统。  
  
此外,该 PoC 只需要非特权用户命名空间(unprivileged user namespaces)即可。许多 CentOS 和 RHEL 10 系统默认启用了这一功能。因此,在多租户(multi-tenant)和云主机环境中,其攻击面相当广泛。  
  
**攻击原理**  
  
该漏洞是一种 slab 内的线性溢出(in-slab linear overflow)。它会越过数据包缓冲区写入,一直写到位于其尾部的 skb_shared_info  
 结构。  
  
由此开始,攻击链逐步升级。首先,溢出翻转了一个控制字节,并触发页面释放后使用(page use-after-free)。接着,这个被释放的页面以叶级页表(leaf page table)的形式被重新分配回来,从而实现任意内核读写。最后,该利用程序改写进程凭据(credentials),并滥用 core_pattern  
 机制,在宿主机上生成一个 root shell。  
  
值得注意的是,Oldani 记录了攻击机制,但保留了触发部分(trigger)未予公开。他的分析报告阐述了原理,却没有提供一个开箱即用的现成攻击武器。  
  
**测试目标**  
  
Oldani 在两个构建版本上验证了该逃逸。CentOS Stream 10 运行的是内核 6.12.0-242.el10;RHEL 10 运行的是内核 6.12.0-228.el10。两者最终都获得了宿主机 root 权限。  
  
**受影响版本**  
  
该 PoC 针对运行 6.12.x 内核分支的 CentOS 和 RHEL 10。它还需要满足五级分页(5-level paging)以及默认的 init_on_alloc  
 设置。此外,作者指出,其他内核日后可能会被另一种技术攻破。  
  
**补丁与缓解措施**  
  
上游(upstream)修复已于 2026 年 6 月 16 日以提交(commit)736b380e28d0 的形式合入。该修复纠正了分页分配路径上的分片运算逻辑。因此,第一步很简单:升级到已打补丁的内核。  
  
如果无法立即打补丁,应限制非特权用户命名空间。将 user.max_user_namespaces=0  
 设为该值即可阻断已被记录的攻击路径。  
  
**利用现状**  
  
由于作者本人公开发布,目前已存在公开的概念验证(PoC)。不过,尚未确认 IPV6_FRAG_ESCAPE 存在任何在野利用(in-the-wild)的情况。尽管如此,防御者仍应尽快打补丁,因为公开代码会降低攻击者的门槛。  
  
参考链接：  
  
https://github.com/sgkdev/ipv6_frag_escape  
  
