#  CNNVD关于Citrix NetScaler Application Delivery Controller 输入验证错误漏洞的通报  
原创 CNNVD
                    CNNVD  CNNVD安全动态   2026-09-28 08:58  
  
![图片](https://mmbiz.qpic.cn/mmbiz_gif/g1thw9GoocfpeKv1eicF4icEx1vUX4LQ1JjlMnGl5z2XiaAQGZdFulYs0vsE3icB8RUiawPqDSb5lvm8G0drb7iaw7sQ/640?wx_fmt=gif&from=appmsg "")  
  
![图片](https://mmbiz.qpic.cn/mmbiz_gif/g1thw9GoocfpeKv1eicF4icEx1vUX4LQ1Js3VkKswpUtkoDWibZ1YQl1lIdcctfqePCcSPEdc38SnhJGdqGJUFx9w/640?wx_fmt=gif&from=appmsg "")  
  
**点击蓝字 关注我们**  
  
![图片](https://mmbiz.qpic.cn/mmbiz_gif/g1thw9GoocfpeKv1eicF4icEx1vUX4LQ1Js3VkKswpUtkoDWibZ1YQl1lIdcctfqePCcSPEdc38SnhJGdqGJUFx9w/640?wx_fmt=gif&from=appmsg "")  
  
  
**漏洞情况**  
  
近日，国家信息安全漏洞库（CNNVD）收到关于Citrix NetScaler Application Delivery Controller 输入验证错误漏洞（CNNVD-2026-87241110、CVE-2026-88771）情况的报送。未经身份验证的攻击者可通过网络向目标设备发送恶意请求，进而执行任意代码。Citrix多个版本均受此漏洞影响。目前，Citrix官方已发布新版本修复了该漏洞，建议用户及时确认产品版本，尽快采取修补措施。  
  
## 一漏洞介绍  
  
  
Citrix NetScaler Application Delivery Controller是美国Citrix公司的一款应用交付与负载均衡产品，用于实现应用流量管理、负载均衡、应用加速及安全防护等功能。该漏洞源于输入验证不当，未经身份验证的攻击者可通过网络向受漏洞影响的设备发送恶意请求，进而执行任意命令。  
  
  
## 二危害影响  
  
  
Citrix NetScaler Application Delivery Controller 14.1版本至14.1-73.37之前版本、13.1版本至13.1-64.23之前版本、FIPS 14.1-73.37之前版本、FIPS 13.1.37.279之前版本和NDcPP 13.1.37.279之前版本以及Citrix NetScaler Gateway 14.1版本至14.1-73.37之前版本、Citrix NetScaler Gateway 13.1版本至13.1-64.23之前版本均受此漏洞影响。  
  
  
## 三修复建议  
  
  
目前，Citrix官方已发布新版本修复了该漏洞，建议用户及时确认产品版本，尽快采取修补措施。官方参考链接：  
  
https://support.citrix.com/support-home/kbsearch/article?articleNumber=CTX697096  
  
本通报由CNNVD技术支撑单位——奇安信网神信息技术（北京）股份有限公司、中国信息安全测评中心华中测评中心等技术支撑单位提供支持。  
  
CNNVD将继续跟踪上述漏洞的相关情况，及时发布相关信息。如有需要，可与CNNVD联系。联系方式: cnnvd@itsec.gov.cn  
  
![图片](https://mmbiz.qpic.cn/mmbiz_gif/g1thw9GoocfpeKv1eicF4icEx1vUX4LQ1JMd8aMOqNkic25xydKvYcCVEsHXvm506icfXiaFep4AfohjraUj3F2jMfg/640?wx_fmt=gif&from=appmsg "")  
  
  
