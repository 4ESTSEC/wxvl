#  新型“Bad Epoll”零日漏洞允许攻击者获取Linux服务器和安卓设备的root权限  
原创 ZM
                    ZM  暗镜   2026-07-07 22:11  
  
最新披露的 Linux 内核漏洞被称为“Bad Epoll”（CVE-2026-46242），它允许非特权本地用户通过利用内核 epoll 子系统中的竞争条件和释放后使用 (UAF)漏洞，在 Linux 服务器、桌面和 Android 设备上提升到 root 权限。  
  
Bad Epoll 是 ep_remove() 中的一个 UAF 漏洞，它会清除 file->f_lock 下的 file->f_ep，但在 hlist_del_rcu() 和 spin_unlock() 期间继续使用临界区内的文件对象。  
  
并发的 `__fput()` 调用可以检测到瞬态 NULL 值，跳过 `eventpoll_release_file()`，直接执行 `f_op->release`，释放一个仍在使用的、被监视的 `struct eventpoll` 结构体，从而破坏内核内存。由于 `struct file` 结构体是 `SLAB_TYPESAFE_BY_RCU` 类型安全的，被释放的内存槽也可以被 `alloc_empty_file()` 回收利用，这使得攻击者可以针对错误的 slab 缓存触发 `kmem_cache_free()` 释放操作。  
  
该漏洞由研究员 Jaeyoung Chung 发现并利用，他将其作为零日漏洞提交给了 Google 的 kernelCTF 计划，该计划为有效的 Linux 内核漏洞利用程序支付 71,337 美元或更多奖金。  
  
与大多数 Linux 权限提升漏洞不同，Bad Epoll 可以获取 Android 的 root 权限，因为 epoll 是一个核心内核组件，无法禁用或卸载，这与Copy Fail 等漏洞利用的可选模块不同。它还可以从 Chrome 的渲染器沙箱内部访问，这使得利用渲染器漏洞和 Bad Epoll 漏洞执行完整内核代码成为可能。尽管竞争窗口只有大约六条指令宽，但 Chung 的漏洞利用程序扩大了窗口并进行了重试，而不会导致内核崩溃，在测试目标上达到了约 99% 的可靠性。  
  
2023 年的一次内核提交在同一条 2500 行的 epoll 代码路径中引入了两个不同的竞态条件。第一个竞态条件 CVE-2026-43074 由 Anthropic 的 AI 模型 Mythos 发现，这表明前沿 AI 在发现内核竞态漏洞方面的能力日益增强。  
  
第二个难以发现的缺陷是 Bad Epoll，Mythos未能发现它，可能是因为其触发时间窗口很窄，而且很少触发内核的主要内存错误检测器 KASAN，因此运行时几乎没有留下任何痕迹。维护者的第一个补丁尝试并未完全解决问题，最终的正确修复方案在首次披露近两个月后才发布。  
  
该漏洞利用了四个 epoll 对象，分为两对；关闭其中一对会触发竞争，而另一对则成为受害者对象，通过跨缓存攻击将 8 字节的 UAF 写入转换为对文件对象的 UAF。  
  
攻击者由此通过 /proc/self/fdinfo 获得任意内核内存读取权限，并使用面向返回的编程 (ROP) 链劫持控制流，从而获得 root shell。  
  
由于禁用 epoll 会破坏操作系统和浏览器的核心功能，因此没有变通办法；管理员必须应用上游补丁或等待发行版向后移植。  
  
  
