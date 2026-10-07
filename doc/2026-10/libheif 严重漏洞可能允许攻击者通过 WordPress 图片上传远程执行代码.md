#  libheif 严重漏洞可能允许攻击者通过 WordPress 图片上传远程执行代码  
原创 ZM
                    ZM  暗镜   2026-10-06 22:00  
  
libheif 中存在一个严重的堆缓冲区溢出漏洞，已通过身份验证的 WordPress 用户可以通过标准媒体库工作流程上传特制的 HEIC 图像来实现远程代码执行。  
  
该问题（编号为 GHSA-x8r2-mggj-j6wr）影响库的未压缩图像（unci）解码器，已在 libheif 1.23.3 中修复。  
  
在受影响的 WordPress 部署中，上传的 HEIC 文件可以通过 PHP 的 Imagick 扩展和 ImageMagick 从 WordPress 传递到 libheif，其中易受攻击的图像解析发生在 PHP-FPM 工作进程中。  
  
libheif 的混合交错 YCbCr 解码路径存在缺陷。恶意图像可以将 Cb 色度分量声明为 16 位，同时将 Cr 色度分量声明为 8 位。  
  
尽管 libheif 为每个样本分配一个字节的 Cr 图像平面，但易受攻击的代码在写入两个色度通道时使用了 Cb 分量的两字节宽度。  
  
这将导致超出 Cr 分配范围的文件控制越界写入，从而造成堆损坏。  
  
Fortbridge 的研究人员证明，在两个高度特定的 WordPress 环境中，内存损坏可以链接到经过身份验证的远程代码执行：Ubuntu 26.04，WordPress 7.1.1，PHP-FPM 8.5.4，ImageMagick 7.1.2.18 和 libheif 1.21.2；以及 Debian 13，WordPress 7.0，PHP-FPM 8.4.24，ImageMagick 7.1.1.43 和 libheif 1.19.8。  
  
概念验证程序www-data在 WordPress 作者上传恶意 HEIC 内容后，以 PHP-FPM 帐户身份执行了命令。  
  
攻击需要已通过身份验证的 WordPress 帐户upload_files，且该帐户必须具有相应的权限，通常是作者级别或更高级别的角色。  
  
该攻击链并非依赖于单一的恶意上传。它首先利用特制的 HEIC 镜像，从 WordPress 生成的衍生镜像中泄露地址信息。  
  
WordPress 将上传的文件处理并重新压缩为 JPEG 格式后，这些衍生图像可以通过像素暴露内存内容。  
  
Fortbridge 表示，此次披露是基于 libheif 的一个独立缺陷 GHSA-2jg2-4ch7-h545，该缺陷涉及派生图像和像素平面处理中的越界读取和写入。  
  
该问题影响到 1.23.1 版本，并在 libheif 1.23.2 中得到修复。  
  
通过从返回的像素中重建泄露的值，该漏洞利用程序可以识别已加载的模块地址，包括 libc、libheif 和 ImageMagick 组件。  
  
然后，它会选择一个与目标服务器的操作系统、库版本、分配器行为和对象布局完全匹配的配置文件，然后再生成 ASLR 调整后的unci有效载荷。  
  
这一要求使得已发布的区块链具有很强的环境特异性，而不是普遍可靠性。  
  
