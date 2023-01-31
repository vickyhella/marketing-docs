# 使用 BCI 的五个理由

现在，软件开发的范例是将容器化应用程序部署到 Pod 上，然后让 Kubernetes 进行管理。Kubernetes 可以管理应用程序的部署、复制、HA、指标和其他功能，以便应用程序能专注于执行主要功能。该技术广泛用于全球各地的项目和客户。

要容器化应用程序，你需要使用镜像，而镜像通常是基于语言的（Golang、Python、Rust、.NET 等）。镜像提供商有很多，包括将镜像提交到镜像仓库的个人贡献者，以及企业级镜像提供商等。你也可以在开发中使用基于操作系统的镜像。

我与软件工程师进行过一次有趣的讨论，主题是：**根据需求选择容器镜像的主要问题是什么？**

在本文中，我将说明并解决软件工程师向我表达的一些问题。

## 软件工程问题

- 安全性：如何确保使用的版本没有受到 CVE 或其他漏洞的影响？

- 重新分发：需要部署应用程序，但是不了解底层主机、架构或编排技术。
- 仓库和工具：有时我需要使用一些工具（Git、编译器、库等），但大多数镜像不提供可以获取这些工具和包的可靠仓库。
- 可用性：开源贡献者和企业软件工程师都需要基于多种平台和架构（ARM、x86_64 等）制作软件。镜像不仅要适应架构，还要适用于不同语言规范。有没有可靠的镜像仓库可以作为获取最新镜像的单一参考点，以便维护镜像的标签历史？
- 支持和生命周期：在大多数情况下，不受支持的镜像对于企业级软件来说是无效的，因此需要有供应商为用来创建或容器化应用程序的基础提供支持，ISV/IHV 甚至需要更多支持。此外，镜像必须在生命周期内进行更新、修复安全漏洞和提高性能。

## 使用 BCI 解决这些问题

SUSE 发布了 BCI（Base Container Image），BCI 是专注于安全性、可用性、敏捷性和开发者体验的企业级容器镜像。BCI 不仅基于操作系统，还包括语言包、busybox、base 和 minimal。因此，BCI 是应对各类开发（包括社区项目和生产级应用程序）挑战的理想选择。

BCI 能帮助你解决以下问题：

### 安全

没有人愿意使用受 CVE 影响的软件来构建应用程序。作为开发人员，使用安全镜像可以确保开发的应用程序是可靠的。以下是一些例子：

[Trivy](https://github.com/aquasecurity/trivy) 是一款镜像扫描软件，可用于检查漏洞。

```
➜  ~ trivy image registry.suse.com/bci/bci-base

2022-10-09T03:47:42.403+0200 INFO Need to update DB
2022-10-09T03:47:42.404+0200 INFO DB Repository: ghcr.io/aquasecurity/trivy-db
2022-10-09T03:47:42.404+0200 INFO Downloading DB...
34.35 MiB / 34.35 MiB [-------------------------------------------------------------------------------------------------------] 100.00% 23.13 MiB p/s 1.7s
2022-10-09T03:47:45.503+0200 INFO Vulnerability scanning is enabled
2022-10-09T03:47:45.503+0200 INFO Secret scanning is enabled
2022-10-09T03:47:45.503+0200 INFO If your scanning is slow, please try '--security-checks vuln' to disable secret scanning
2022-10-09T03:47:45.503+0200 INFO Please see also https://aquasecurity.github.io/trivy/v0.32/docs/secret/scanning/#recommendation for faster secret detection
2022-10-09T03:47:45.528+0200 INFO Detected OS: suse linux enterprise server
2022-10-09T03:47:45.528+0200 INFO Detecting SUSE vulnerabilities...
2022-10-09T03:47:45.528+0200 INFO Number of language-specific files: 0
registry.suse.com/bci/bci-base (suse linux enterprise server 15.4)
Total: 0 (UNKNOWN: 0, LOW: 0, MEDIUM: 0, HIGH: 0, CRITICAL: 0)
```

**bci-base** 扫描结果

bci-micro、bci-minimal、buxybox 和 init 上的结果也是一样的。

```
➜  ~ trivy image registry.suse.com/bci/bci-micro

[…]
2022-10-09T03:55:17.952+0200 INFO Detecting SUSE vulnerabilities...
2022-10-09T03:55:17.953+0200 INFO Number of language-specific files: 0
registry.suse.com/bci/bci-micro (SUSE Linux enterprise server 15.4)

Total: 0 (UNKNOWN: 0, LOW: 0, MEDIUM: 0, HIGH: 0, CRITICAL: 0)
```

**bci-micro, bci-init, bci-buxybox** 扫描结果

这可以通过维护镜像的生命周期来实现，从而确保对 [CVE Mitre 数据库](https://cve.mitre.org/)中发布的每个新漏洞做出反应。

### 重新分发

对于 ISV/IHV 软件，应用程序必须进行更多的重新分发。这里有两个问题：

- EULA 协议：

根据 BCI EULA，重新分发应用程序唯一的限制是第三方软件或与订阅绑定的资源，例如，私有仓库的包或基于许可证的硬件提供商。使用这些镜像开发的应用程序都会被重新分发。

- 软件：

BCI 带有一个仓库，你可以根据需要将包添加到镜像中，这样能增加自定义镜像的灵活性，同时让应用程序可以重新分发（不需要添加外部依赖项）。

### 仓库和工具

永恒的讨论：在满足开发人员的需求和尽量让镜像小而简单之间，平衡的中间点是什么？

在涉及企业级镜像以及开发人员体验的时候，安全性是一个主要问题。BCI 使用每个镜像都可以访问的仓库来提供工具、编译器和其他有用的软件，从而解决这个问题。有了 Zypper，开发人员可以根据应用程序的需要在镜像安装包上增加一层。

你不需要手动添加仓库、查找包、检查和解决依赖项，或者依赖外部软件和包源。

这些仓库可以免费使用，而且 SUSE 会维护自己的包。

### 可用性

BCI 支持多种架构，包括 AArch64、ppc64le、s390x 和 x86_64，因此，无论你创建的是哪种类型的应用程序（边缘、物联网、传感器、设备，网页，微服务等），都可以轻松地将基础镜像集中在 BCI 上。

BCI 还为使用特定语言编写的应用程序提供基于语言的镜像，包括 Golang、Python、Rust、.NET、OpenJDK、NodeJS 等。

### 支持和生命周期

SUSE 设计了 ​​BCI，而镜像通过企业级、ISV 和 IHV 的订阅获得商业支持。支持矩阵涵盖了 BCI 和语言包的基础，你可以在 [BCI 镜像仓库](https://registry.suse.com/)查看支持详情。

BCI 也是一个开源项目，你可以查看它的实际开发状态，了解它们是如何针对不同架构进行构建的，以及查看其他相关信息。

这些镜像使用了 Open Build Service (OBS)，每个人都可以使用 OBS。

- [SLE 15 SP4 的 BCI 开发项目](https://build.opensuse.org/project/show/devel:BCI:SLE-15-SP4)
- [SLE 15 SP3 的 BCI 开发项目](https://build.opensuse.org/project/show/devel:BCI:SLE-15-SP3)

### 结论

除了镜像组合之外，你还有很多其他选择。SUSE 提供了以安全为中心的产品，同时也注重开发人员的体验。

BCI 提供了一个灵活的容器镜像生态系统，而且尽可能地减少占用空间。此外，我们还提供了一种灵活扩展镜像的机制，让开发者可以专注于应用程序本身。

通过订阅，ISV 和 IHV 可以利用他们的应用程序，为应用程序使用的基础镜像提供 SUSE 企业级支持。

由于 BCI 可以在支持矩阵内在任何类型的主机中部署和使用，因此重新分发是没有问题的，任何人都可以在不受更多限制的情况下访问镜像，构建和重新分发应用程序。

包仓库可以为开发人员提供灵活性，不会增加任何类型的限制。SUSE 是一家行业领先的可靠的厂商，会维护这些软件包以及包和镜像的集成。

如需了解更多信息，请查看以下资源：

[BCI 文档](https://documentation.suse.com/smart/linux/html/concept-bci-get-started/index.html)

[常见问题](https://www.suse.com/products/base-container-images/FAQ/)

[SUSE 容器镜像](https://registry.suse.com)
