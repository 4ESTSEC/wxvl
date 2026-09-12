#  Next js 远程代码执行  
 Khan安全团队   2026-09-12 06:20  
  
使用 AVIF 文件时，图像优化 API 中存在未经身份验证的远程代码执行问题。  
```
127.0.0.1:3000/_next/image?url=/poc.avif&w=128&q=75
```  
  
在修复程序发布之前，AVIF 文件的优化功能将被禁用。  
  
![](https://mmbiz.qpic.cn/mmbiz_jpg/J7CSmJcRR8lo8iaIHkLBnbDzme7enyInnMh3kbgMHeg9d9GyPGthBIOO4ciaBag418620o35wKgonzb4Cs8qpPibibsQeDEgV8cXTdBFV2ia66ibU/640?wx_fmt=jpeg "")  
  
  
