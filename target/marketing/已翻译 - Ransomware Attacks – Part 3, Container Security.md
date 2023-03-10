# 勒索软件攻击 Part 3：容器安全

## 介绍

本文是勒索软件攻击系列的第三篇文章，我将探讨如何保护 Kubernetes 环境。

## 如何让 Kubernetes 环境免受勒索软件攻击？

随着容器使用率的增长，容器化软件逐渐成为了勒索软件攻击的目标，而 Kubernetes 环境本身的动态特性也带来了独特的挑战。勒索软件的传播速度可能非常快，可能会感染镜像仓库或软件供应链的其他内容，导致 Kuberentes 集群中的所有 Pod 遭到破坏。攻击者还可能控制 Kubernetes 集群，使攻击难以停止。

正如本系列的第一篇文章所述，攻击可能通过应用程序漏洞、Kubernetes 集群被盗的凭证、软件供应链中植入的恶意软件等发生。要让 Kubernetes 环境免受攻击，采取预防措施非常重要。

某些攻击只能通过使用特定的安全软件和预定义流程解决。

要防止通过应用程序漏洞进行的攻击，我们可以使用一些策略：

- **让软件保持更新，使用已修复错误的最新版本**。要确保没有在集群上部署易受攻击的软件，你可以使用 [SUSE NeuVector](https://blog.neuvector.com/article/kubernetes-admission-control) 来实现准入控制规则，它扩展了 Kubernetes 的功能，允许你定义规则（例如，防止将具有漏洞或来自不受信任的镜像仓库的镜像部署到集群中）。

- 在攻击抵达应用程序之前，在网络级别主动**阻止已知攻击**。[SUSE NueVector](https://neuvector.com/solutions/application-security-solutions/) 特别适合执行此任务，因为它不仅关注其他安全解决方案关注的第 3 和第 4 网络层协议，也关注包含应用程序协议的第 7 网络层。

换言之，它可以使用[深度包检测](https://blog.neuvector.com/article/web-application-firewall) (DPI) 技术识别通过应用程序层携带的攻击，并且在恶意网络数据包抵达应用程序之前阻止它们。

- **限制应用程序权限**。你可以通过 [Kubernetes 安全机制](https://kubernetes.io/docs/concepts/security/)（例如 [Seccomp](https://lwn.net/Articles/656307/)、AppArmor 和 SELinux）进一步防止勒索软件攻击。正如本系列前文所述，SELinux 和 AppArmor 可以有效防止应用程序访问某些文件或运行某些进程。它们只在启用了这些功能的 Linux 发行版上运行的 Kubernetes 节点中可用，例如 [SLES](https://documentation.suse.com/sles/html/SLES-all/cha-selinux.html) 和 [SLEmicro](https://documentation.suse.com/en-us/sle-micro/html/SLE-Micro-all/cha-selinux-slemicro.html)。

## 为什么使用[零信任](https://www.suse.com/campaigns/zero-trust/)策略来阻止恶意软件的传播？

这些措施可能都无法达到要求，尤其是攻击者使用零时差攻击来获取访问权限的情况。

要防止[零时差攻击](https://en.wikipedia.org/wiki/Zero-day_(computing))，我们需要实施[基于行为的零信任](https://www.suse.com/c/another-orchestrated-attack-how-do-i-protect-myself/)策略，该策略可以观察并阻止在网络或系统层上试图偏离预期的应用程序行为。换言之，即使攻击者获得了应用程序的控制权，他们也无法使用它来访问集群中的其他应用程序。通常情况下，大多数后端应用程序不会公开，安全保护比较弱，有些应用程序还具有比较高的系统权限。因此，如果能控制这些应用程序的网络或数据访问，攻击者就有机会进一步进行传播。

在这种情况下，我们可以使用 SUSE NeuVector 的零信任安全策略，因为我们不仅仅要查看已知的攻击，还需要查看应用程序的行为。为此，我们需要定义一个安全策略，听起来可能很复杂，但是我们可以借助 [NeuVector 行为学习](https://neuvector.com/why-neuvector/use-cases/security-automation/)功能，轻松为每个正在运行的应用程序创建策略，在[这篇文章](https://www.suse.com/c/another-orchestrated-attack-how-do-i-protect-myself/)中，我展示了如何使用 NeuVector 行为学习功能来创建零信任安全策略。

我们不仅可以使用 SUSE NeuVector 来实施系统和网络级别的[零信任](https://www.suse.com/c/rancher_blog/zero-trust-the-new-security-model-for-cloud-native-applications-and-infrastructure/)策略，还可以查看每个 Pod 通过 DPI 技术建立的连接，[可视化和检测 Kubernetes 环境中的可疑活动](https://neuvector.com/solutions/container-visibility-and-monitoring-solutions/)。

## 在云原生环境中拥有安全软件供应链的重要性？

软件供应链是云原生环境的重要组成部分。这是一个端到端的过程，可以确保云原生环境中使用的所有应用程序和组件都是安全的，从运行 Kubernetes 的硬件和操作系统，到最终应用程序上使用的所有库。

为了确保安全，我们需要验证软件的真实性，例如使用受监管的第三方[签名](https://packagehub.suse.com/package-signatures/)，定期修补漏洞，使用安全交付方法，以及控制修改代码的人员和时间，确保所有更改都要经过了审核。

这就是 [SLSA4](https://documentation.suse.com/sbp/server-linux/html/SBP-SLSA4/index.html) 和 [Common Criteria EAL4+](https://www.suse.com/support/security/certifications/) 等认证体现价值的地方，它们确保了用于产品开发和维护的 SUSE 流程通过了这些认证的严格安全要求。

但是，我们不能总是使用经过认证的软件。因此，我们需要使用 [SUSE NeuVector](https://neuvector.com/why-neuvector/use-cases/compliance/) 等安全平台或 [Rancher](https://www.rancher.com/products/rancher) 等 Kubernetes 集群管理器来扫描镜像仓库中的镜像漏洞、验证是否符合安全要求、在基础设施上运行安全 Benchmark 等。更重要的是，这些检查可以重复执行，而且是自动化的，不是一次性的。

## 为什么必须在 Kubernetes 环境中实现安全自动化？

为确保环境安全、合规和更新、通过补丁和配置供应链中的软件资源来减少攻击面，Kubernetes 环境中的安全自动化至关重要。

由于云原生环境中的安全性不是静态的，因此每天都会出现新的漏洞和补丁，犯罪分子会自动进行攻击，快速发现并攻击受害者。

自动化还带来了其他好处，例如避免手动错误，以及确保在整个环境中始终执行安全策略。

此外，它还可以大幅度缩短将受攻击系统恢复到原始状态所需的时间。但是需要特别注意的是，在恢复系统之前必须先打补丁。为此，我们还需要简化和自动化更新过程。

为了实现和简化该操作，SUSE NeuVector 等安全平台和 Rancher 等 Kubernetes 管理工具在设计时就考虑到了自动化。借助 [Rancher](https://ranchermanager.docs.rancher.com/)，我们可以使用 [Fleet](https://ranchermanager.docs.rancher.com/v2.6/pages-for-subheaders/fleet-gitops-at-scale) 或其他[外部 CI/CD 系统](https://www.suse.com/c/rancher_blog/managing-rancher-resources-using-pulumi-as-an-infrastructure-as-code-tool/)跨集群自动部署和升级工作负载，简化 Infrastructure-as-Code 的使用。

借助 SUSE [NeuVector API 和 CRD](https://blog.neuvector.com/article/kubernetes-policy-as-code-crd)，你可以像加载 Kubernetes 中的其他资源一样加载安全策略，让使用现有 CI/CD 平台实现 Security-as-Code 变得简单。

## 具有多个集群时，如何扩展这些措施？

我们需要使用小而可靠的软件，因为高资源消耗将影响应用程序性能，导致应用程序无法满足不断增长的需求，这也是生产就绪软件需要关注的重点之一。

我们可以使用 Rancher 的安全功能来确保所有 Kubernetes 集群都得到正确的配置和保护。Rancher 可以帮助我们控制访问、实施安全策略和扫描漏洞。

有了 SUSE [NeuVector](https://open-docs.neuvector.com/navigation/multicluster)，我们还可以通过将联合规则推送到各个集群，并将镜像仓库扫描结果同步到多个集群，从而管理多集群和多云部署的安全性。

即使有多个集群，我们也可以使用该方法扩展安全措施，NeuVector 架构可以轻松地随应用程序工作负载扩展保护。

## 摘要

我们可以看到，拥有安全的软件供应链并在系统和网络层实施基于行为的零信任安全策略可以帮助用户抵御恶意攻击（例如勒索软件和零时差攻击）。*SUSE 产品旨在优先考虑安全性和大规模工作，我们的团队[在安全领域不断创新](https://www.suse.com/support/security/)，确保你的业务保持稳定和弹性*。

*如需进一步了解零信任，你可以免费下载我们的[零信任容器安全](https://more.suse.com/zero-trust-security-for-dummies.html)电子书，或者随时申请 [NeuVector Demo](https://more.suse.com/neuvector-security-demo)。*

*如需进一步了解我们产品和服务，请[联系我们](https://www.suse.com/contact/)。*

*如需阅读本系列的其他文章，请访问：*

[勒索软件攻击 Part 1：简介](https://www.suse.com/c/ransomware-attacks-part-1-introduction)

[勒索软件攻击 Part 2：传统 IT 安全](https://www.suse.com/c/ransomware-attacks-part-2-traditional-it)

[如何避免 SAP 应用程序受到勒索软件攻击](https://www.suse.com/c/ransomware-attacks-part-4-sap-applications/)