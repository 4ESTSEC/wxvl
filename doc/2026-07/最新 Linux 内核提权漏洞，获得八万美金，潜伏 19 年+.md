#  最新 Linux 内核提权漏洞，获得八万美金，潜伏 19 年+  
原创 i3eg1nner
                    i3eg1nner  SecureNexusLab   2026-07-07 03:38  
  
2007年，有人往 Linux 内核里提交了一个 commit。  
  
直到2026年，来自 GMO Cybersecurity / Ierae Security 的两位日本研究员 Yuki Koike 和 Kota Toda 把它挖了出来，交给 Google 的 kernelCTF，拿走了超过8万美金的奖励。  
  
这个漏洞是 CVE-2026-43456，在 Linux 2.6.24 到 6.12.77 之间的所有版本里都有，影响范围大得惊人。根因在 net/bonding  
 子系统，是一个类型混淆，触发只需要 CAP_NET_ADMIN  
 权限，一旦触发，提权成功率超过99%，全程不超过一秒，目前暂无公开EXP。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/PkfClzhSYicxibg9yLXiaeWyYtLLCLIAX3uN1W2rsJQCmdN5GxuMMTqeSnGmial3wztVFdCKHoU8muotoYZX3Q65qWRLzibbyXLn3dQNc37DApac/640?wx_fmt=png&from=appmsg "")  
## 先说说 bonding 和 skb 是什么  
  
在开始拆漏洞之前，需要先把两个基础概念交代清楚，不然后面很多东西会看不懂。  
  
**「bonding」**  
 是 Linux 的一个网络特性，允许把多张网卡绑在一起，对外表现成一张虚拟网卡，常见于高可用或负载均衡场景。这张虚拟接口叫 bond device，底下挂着的物理或虚拟接口叫 slave device。  
  
**「skb」**  
 是 struct sk_buff  
 的简称，Linux 内核网络栈里所有数据包都用这个结构来表示。它的内存布局大概是这样，  
  
![](https://mmbiz.qpic.cn/mmbiz_png/PkfClzhSYicw4qIQ6VTA294ttcB01OSkbESzicoyg9mNq5mRuAorBTsRibNgTd7yOX1TvFPTyGmibeP2g7e1CevcgnFjSZ2KGnbf19WtTo5jz2w/640?wx_fmt=png&from=appmsg "")  
  
skb->head  
 是分配的 buffer 起点，skb->data  
 是当前包数据的起点，skb->tail  
 是当前包数据的终点。skb->data - skb->head  
 就是 headroom 的大小。  
  
在 skb buffer 的末尾，紧跟着一个 struct skb_shared_info  
，里面的 flags  
 字段存储着这个 skb 的一些状态，比如 zerocopy 是否开启。  
  
这两个概念记住了，我们就可以开始看漏洞了。  
## 那一行赋值  
  
漏洞的根因在 bond_setup_by_slave  
 这个函数里。当一个 slave device 被挂到 bond 上时，内核会调用这个函数，把 slave 的一些属性复制给 bond device，让 bond 「变成」slave 的样子。  
  
代码如下，注意打了 ★ 的那一行，  
```
ounter(lineounter(lineounter(lineounter(lineounter(lineounter(lineounter(lineounter(lineounter(lineounter(lineounter(lineounter(lineounter(line
static void bond_setup_by_slave(struct net_device *bond_dev,
                struct net_device *slave_dev)
{
    bool was_up = !!(bond_dev->flags & IFF_UP);

    dev_close(bond_dev);

 ★ bond_dev->header_ops      = slave_dev->header_ops;  

    bond_dev->type            = slave_dev->type;
    bond_dev->hard_header_len = slave_dev->hard_header_len;
    bond_dev->needed_headroom = slave_dev->needed_headroom;
    bond_dev->addr_len        = slave_dev->addr_len;
```  
  
header_ops  
 是一个函数指针表，里面装的是处理某种协议包头的一组函数，包括「怎么创建包头」「怎么解析包头」等等。  
  
看起来很合理，bonding 的设计初衷就是透明代理 slave，那么处理包头自然也要用 slave 那套函数。  
  
但问题就在这里，这些函数执行的时候，会通过 netdev_priv(dev)  
 拿到设备的私有存储区，然后对着它做读写。bond device 的私有区类型是 struct bonding  
，slave device 的私有区类型是别的，比如你用 GRE 做 slave，那就是 struct ip_tunnel  
。  
  
这两个结构体完全不兼容，字段的位置和含义都对不上。  
  
把 slave 的 header_ops  
 函数挂到 bond 上之后，一旦那些函数被调用，它们会通过 bond 的 netdev_priv(dev)  
 拿到的是 struct bonding  
，但它们以为自己拿到的是 struct ip_tunnel  
，于是读到了错误的数据，写到了错误的位置。  
  
类型混淆，就这一行赋值。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/PkfClzhSYicySMBbYw8gPJW7lL2DVfbXmmRYzr6PumfuQAb6s6GFpQgH116zupAlYO1hZFAuZ7VCw25icQBZnzRItElgeTMrSnaibphoznnS8I/640?wx_fmt=png&from=appmsg "")  
  
把两个结构体的定义放在一起看一眼就清楚了，  
```
ounter(lineounter(lineounter(lineounter(lineounter(lineounter(lineounter(line
struct bonding {
    struct net_device *dev;                /* 偏移 0x00 */
    struct slave __rcu *curr_active_slave;
    struct slave __rcu *current_arp_slave;
    struct slave __rcu *primary_slave;
    ...
    int (*recv_probe)(...);                /* 偏移 0x38，函数指针 */
```  
```
ounter(lineounter(lineounter(lineounter(lineounter(lineounter(lineounter(lineounter(lineounter(line
struct ip_tunnel {
    struct ip_tunnel __rcu *next;          /* 偏移 0x00 */
    struct hlist_node hash_node;
    struct net_device *dev;
    netdevice_tracker dev_tracker;
    struct net *net;
    unsigned long err_time;
    ...
    struct in6_addr laddr;                 /* 偏移 0x38，IPv6 地址 */
```  
  
偏移 0x38  
 这个位置，在 bonding 里是一个函数指针 recv_probe  
，在 ip_tunnel / ip6_tnl 里是一个 IPv6 源地址 laddr  
。  
  
这个巧合，是整个 exploit 的关键。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/PkfClzhSYiczuJ1gVwwrku6Y5oOeicZwIPtibhytWYl0ZubibyKAOOwdZKqibfuCmj5AjLPge4yZpKC5YqbKAiaJ28jiaNViaNCETVzXZO8UL4tvSdg/640?wx_fmt=png&from=appmsg "")  
## Step 1，KASLR 泄露  
  
像用户态一样，Linux 内核也有地址随机化，叫 KASLR。要做稳定的漏洞利用，第一步得先把内核基地址算出来，这个过程叫 leak。  
  
研究员用的是 IP6GRE，IPv6 版本的 GRE 协议。  
  
当 IP6GRE 设备作为 slave 挂到 bond 上之后，类型混淆就已经发生了，bond 的 dev->priv  
 是 struct bonding  
，但 IP6GRE 的 header_ops  
 函数以为自己访问的是 struct ip6_tnl  
。  
  
从 struct ip6_tnl  
 的角度来看，偏移 0x38  
 是 parms.laddr  
，也就是 IPv6 源地址，  
```
ounter(lineounter(lineounter(lineounter(lineounter(line
/* offset | size */  type = struct ip6_tnl {
/* 0x0000 | 0x0008 */    struct ip6_tnl *next;
...
/* 0x0034 | 0x0004 */    __u32 flags;
/* 0x0038 | 0x0010 */    struct in6_addr laddr;   // IPv6 源地址
```  
  
但从 struct bonding  
 的角度来看，偏移 0x38  
 是 recv_probe  
，一个函数指针，  
```
ounter(lineounter(lineounter(lineounter(line
/* offset | size */  type = struct bonding {
/* 0x0000 | 0x0008 */    struct net_device *dev;
...
/* 0x0038 | 0x0008 */    int (*recv_probe)(...);   // 函数指针
```  
  
当 ARP 监控开启时，内核会把 recv_probe  
 赋值为 bond_rcv_validate  
，  
```
ounter(lineounter(lineounter(lineounter(line
if (bond->params.arp_interval) {
    queue_delayed_work(bond->wq, &bond->arp_work, 0);
    bond->recv_probe = bond_rcv_validate;
}
```  
  
bond_rcv_validate  
 是内核里的一个函数，地址固定，偏移已知。  
  
当 IP6GRE 的函数把这个函数指针当作 IPv6 源地址 laddr  
 写进发出的数据包时，研究员就能从接收到的包里读到这个值，然后减去 bond_rcv_validate  
 在镜像里的固定偏移，内核基地址就计算出来了。  
  
KASLR 绕过完成。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/PkfClzhSYiczDBW4lMTwX6yWDgkQdH21ticdW8yiaRwzWyFvmiaricibib7hN5HtMSfsqmuVaaj8qB4BrJTMkTzkZayMCEwWSKcn9dHjCYRsNXNp4Y/640?wx_fmt=png&from=appmsg "")  
## Step 2，任意代码执行  
  
有了内核基地址，下一步是劫持 Instruction Pointer，实现任意代码执行。  
  
这一步稍微复杂一些，分成两个子步骤。  
### Step 2.1，改写 skb 的 flags  
  
先说结论，目标是让下面这段代码里的 uarg->callback  
 被调用到，  
```
ounter(lineounter(lineounter(lineounter(lineounter(lineounter(lineounter(lineounter(lineounter(lineounter(lineounter(lineounter(line
static inline struct ubuf_info *skb_zcopy(struct sk_buff *skb)
{
    bool is_zcopy = skb && skb_shinfo(skb)->flags & SKBFL_ZEROCOPY_ENABLE;
    return is_zcopy ? skb_uarg(skb) : NULL;
}

static inline void skb_zcopy_clear(struct sk_buff *skb, bool success)
{
    struct ubuf_info *uarg = skb_zcopy(skb);
    if (uarg)
        uarg->callback(skb, uarg, success);   // 这里
}
```  
  
skb_zcopy_clear  
 在内核收发包路径上都会被调用。它的逻辑是，如果这个 skb 是 zerocopy 的（即 SKBFL_ZEROCOPY_ENABLE  
 被置位），就把 uarg->callback  
 调起来。  
  
研究员的思路是，通过类型混淆，非法改写一个 skb 的 skb_shinfo(skb)->flags  
，把 SKBFL_ZEROCOPY_ENABLE = BIT(0)  
 这个 bit 置上，让内核以为这个 skb 是 zerocopy 的，从而触发 uarg->callback  
 的调用。  
  
而 uarg  
 指向的位置已经被提前布置好，那里存着研究员想执行的函数地址。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/PkfClzhSYicwQ6hkJ5iaQEKK2cMe97VDVPBEsicibAicjS020d8qRL4hxarglYVvVl0iarLvEVE8lAlVEael7ts0OJ3rwP9Sz7qWxAkOAPKHEOxdQ/640?wx_fmt=png&from=appmsg "")  
  
改写 skb_shinfo->flags  
 是通过 GRE（IPv4 版本）的 header_ops  
 函数里的 buffer overflow 来实现的，  
```
ounter(lineounter(lineounter(lineounter(lineounter(lineounter(lineounter(lineounter(lineounter(lineounter(lineounter(line
static int ipgre_header(struct sk_buff *skb, struct net_device *dev,
            unsigned short type,
            const void *daddr, const void *saddr, unsigned int len)
{
    struct ip_tunnel *t = netdev_priv(dev);
    struct gre_base_hdr *greh;
    struct iphdr *iph;
    ...
    iph = skb_push(skb, t->hlen + sizeof(*iph));
    greh = (struct gre_base_hdr *)(iph + 1);
    greh->flags = gre_tnl_flags_to_gre_flags(t->parms.o_flags);
```  
  
类型混淆发生后，t  
 指向的是 struct bonding  
 而非 struct ip_tunnel  
。  
  
在正常的 GRE 设备里，t->hlen >= sizeof(*greh)  
，所以 skb_push()  
 会把 skb->data  
 往前移动 sizeof(struct iphdr) + sizeof(struct gre_base_hdr)  
 这么多。但在 struct bonding  
 里，对应位置的字段值是 0  
，所以 skb_push()  
 只移动了 sizeof(struct iphdr)  
。  
  
结果是，greh = iph + 1  
，正好指向了 skb->data  
 的原始位置。  
  
然后 greh->flags = gre_tnl_flags_to_gre_flags(t->parms.o_flags)  
 这行代码，就在那个位置写了一个值。  
  
这就是 buffer overflow 的发生点，greh->flags  
 写到了 skb->data  
 指向的位置。  
  
greh->flags  
 和 skb_shared_info->flags  
 的结构都在偏移 0x00  
，  
```
ounter(lineounter(lineounter(lineounter(lineounter(line
/* struct gre_base_hdr */
/* 0x0000 | 0x0002 */    __be16 flags;

/* struct skb_shared_info */
/* 0x0000 | 0x0001 */    __u8 flags;
```  
  
所以，只要 skb->data  
 恰好落在 skb_shared_info  
 的起点，这次 overflow 写就变成了对 skb_shinfo(skb)->flags  
 的改写。  
  
写入的值，也就是 gre_tnl_flags_to_gre_flags(t->parms.o_flags)  
 这一句，由于类型混淆，t->parms.o_flags  
 实际上读的是 struct bonding  
 偏移 0x6e  
 处的值，那正好是 struct bonding::bond_list.next  
 的第六个字节。  
  
bond_list.next  
 是一个内核指针，高字节始终是 0xff 0xff  
，所以，  
```
ounter(line
gre_tnl_flags_to_gre_flags(0xffff) = 0x07ff
```  
  
SKBFL_ZEROCOPY_ENABLE = BIT(0)  
，而 0x07ff  
 的 bit 0 是1，所以这个 bit 刚好被置上了。  
  
写入的值是固定的，与任何运行时变量无关，这也是为什么这个 exploit 的成功率超过99%。  
### Step 2.2，精确控制 skb->data 的位置  
  
前面说，「只要 skb->data  
 恰好落在 skb_shared_info  
 的起点」。  
  
这个条件不是自然满足的，需要精心构造。  
  
skb buffer 按页大小对齐分配，struct skb_shared_info  
 总是落在一个页的末尾。  
  
具体来说，skb buffer 的分配由下面这段代码触发，  
```
ounter(lineounter(lineounter(lineounter(lineounter(lineounter(line
hlen = LL_RESERVED_SPACE(dev);
tlen = dev->needed_tailroom;
linear = __virtio16_to_cpu(vio_le(), vnet_hdr.hdr_len);
linear = max(linear, min_t(int, len, dev->hard_header_len));
skb = packet_alloc_skb(sk, hlen + tlen, hlen, len, linear,
               msg->msg_flags & MSG_DONTWAIT, &err);
```  
  
当 LL_RESERVED_SPACE(dev)  
 等于 0x3ec0  
 时，skb buffer 大小恰好是 0x4000  
，struct skb_shared_info  
 的偏移就是，  
```
ounter(lineounter(line
skb_shinfo(skb) = skb->head + (0x4000 - sizeof(struct skb_shared_info))
                = skb->head + 0x3ec0
```  
  
当发送一个 len == 0  
 的包时，没有包数据，skb->data  
 也落在 0x3ec0  
 这个偏移，和 skb_shared_info  
 的起点完全重合。  
  
所以问题转化成，怎么让 bond device 的 LL_RESERVED_SPACE(dev)  
 精确等于 0x3ec0  
。  
  
LL_RESERVED_SPACE  
 的定义是，  
```
ounter(lineounter(lineounter(line
#define LL_RESERVED_SPACE(dev) \
    ((((dev)->hard_header_len + READ_ONCE((dev)->needed_headroom)) \
      & ~(HH_DATA_MOD - 1)) + HH_DATA_MOD)
```  
  
研究员的方案是，创建329个 GRE 设备，串成一条链，  
```
ounter(line
if0 <- if1 <- if2 <- ... <- if328
```  
  
前8个是带 FOU 封装的 GRE 设备，后面320个是普通 GRE 设备。GRE 允许嵌套，每一层设备挂载时，ip_tunnel_bind_dev()  
 都会把当前 tunnel->hlen  
 加上下层设备的 tdev->hard_header_len  
 和 tdev->needed_headroom  
 一起累加进 dev->needed_headroom  
，  
```
ounter(lineounter(lineounter(lineounter(lineounter(lineounter(lineounter(line
int hlen = LL_MAX_HEADER;
int t_hlen = tunnel->hlen + sizeof(struct iphdr);

if (tdev)
    hlen = tdev->hard_header_len + tdev->needed_headroom;

dev->needed_headroom = t_hlen + hlen;
```  
  
各种设备的参数如下，  
```
ounter(lineounter(lineounter(line
plain GRE,  tunnel->hlen = 0x04, t_hlen = 0x18, hard_header_len = 0x18
FOU GRE,    tunnel->hlen = 0x0c, t_hlen = 0x20, hard_header_len = 0x20
LL_MAX_HEADER = 0x80
```  
  
前8个 FOU GRE 设备链下来，累计值到达 0x260  
，if8  
 的 needed_headroom  
 变成 0x298  
。  
  
接下来的320个 plain GRE 一路累加，最终 if328  
 的 needed_headroom  
 达到 0x3e98  
。  
  
当最后的 GRE 设备作为 slave 挂到 bond 上时，bond 把这个值复制过来，  
```
ounter(lineounter(line
bond->needed_headroom = 0x3e98
bond->hard_header_len = 0x18
```  
  
带入 LL_RESERVED_SPACE  
 的公式，  
```
ounter(lineounter(lineounter(lineounter(line
LL_RESERVED_SPACE = align_down(0x3e98 + 0x18, 0x10) + 0x10
                  = align_down(0x3eb0, 0x10) + 0x10
                  = 0x3eb0 + 0x10
                  = 0x3ec0
```  
  
精确命中。  
## 它为什么藏了19年  
  
到这里，我们也就明白了这个漏洞为什么能藏那么久。  
  
buffer overflow 确实一直都在发生，只要 GRE 作为 slave 挂到 bond 上，类型混淆就存在。但 overflow 写的目标地址，取决于 skb->data  
 指向哪里。skb->data  
 的偏移由 LL_RESERVED_SPACE(dev)  
 决定，而要让它精确等于 0x3ec0  
，你需要精心构造一条329个设备的链。  
  
在任何正常的业务场景里，没有人会这么做。  
  
如果 LL_RESERVED_SPACE  
 不等于 0x3ec0  
，skb->data  
 和 skb_shared_info  
 的起点就不会重合，overflow 写会落进一块未使用的内存区域。不崩溃，没有任何副作用，KASAN 也检测不到。  
  
就这样悄悄运行了将近20年。  
  
研究员说，他们最初是通过 syzkaller 偶然跑出来一个 crash，才发现了这个漏洞。syzkaller 只是配置做了调整，没有改动代码。crash 的位置是 uarg->callback  
 的调用处，根因是 bonding 里那一行赋值，两处代码相距甚远，RCA 的过程极其艰难。  
## AI 在这里面帮了什么  
  
研究员特意说了，RCA 阶段 AI 帮了他们很多。  
  
那是2025年上半年，当时前沿模型对 Linux 内核这类大型开源代码库的理解已经相当深入，能够在复杂的 RCA 过程中提供有意义的帮助。  
  
研究员说，回头看这段经历，感觉是如今越来越常见的「人类研究员和 AI 一起发现漏洞」这种模式的一个早期案例。  
  
但如果问他们，AI 今天能不能完全独立发现这类漏洞，他们的回答是，哪怕到了2026年，他们仍然有些怀疑。  
  
他们说，期待未来 AI 能自动发现这种复杂漏洞，但在那一天到来之前，人类研究员会继续做这件事。  
## 影响范围和缓解措施  
  
漏洞在2026年3月修复，fix commit 是 950803f7254721c1c15858fbbfae3deaaeeecb11  
，受影响版本是 Linux 2.6.24 到 6.12.77，跨度将近20年。  
  
最直接的办法就是升级到最新内核版本。  
  
如果暂时没法升级，有两个应急方案，  
  
一，把 /proc/sys/kernel/unprivileged_userns_clone  
 设为 0  
，阻止非特权用户获取 CAP_NET_ADMIN  
，代价是 Rootless Docker 这类依赖 userns 的功能会失效。  
  
二，直接禁用 bonding 模块，漏洞完全在这个模块里，禁掉就没事，  
```
ounter(lineounter(line
echo "install bonding /bin/false" > /etc/modprobe.d/disable-bonding.conf
rmmod bonding 2>/dev/null
```  
  
大多数发行版里 bonding 默认不启用，这个方案的副作用很小。  
  
  
