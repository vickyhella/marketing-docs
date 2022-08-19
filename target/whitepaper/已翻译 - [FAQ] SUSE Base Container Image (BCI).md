# BCI 常见问题解答（审核时间 2022/04/21）

## What?

### SUSE 宣布了什么？

SUSE BCI（Base Container Image）提供了一个基于 SUSE Linux Enterprise Server 的经过测试和认证的容器镜像仓库，仓库中容器镜像是现成可供企业使用的。SUSE 会定期维护这些镜像，你可以放心使用。我们会使用最新的安全补丁来更新镜像，它们的功能与基本操作系统版本一致。

Rancher 2.6 发布后，SUSE 宣布完全集成 Rancher 和 BCI，并且确保符合最新的安全标准。

### 什么是 BCI？

BCI 是由 SUSE 提供的**基础容器镜像**，全称是 Base Container Image，你能使用 BCI 来根据需求创建应用程序。

SUSE 的容器镜像和应用程序开发工具是真正开放、灵活和安全的，开发人员、集成商和操作人员可以随时使用。

你可以从 [SUSE Con​​tainer Registry](https://registry.suse.com/) 获取 BCI 镜像，并根据 EULA 自行进行复制、使用和分发。

### BCI 包含什么？

BCI 包含两组容器镜像：

1. 单纯基于 SLE 的容器，容器具有最小软件包集，其中一个带有 Zypper，一个不带 Zypper 但带有 RPM，另一个不带 Zypper 和 RPM，这增加了开发环境的灵活性，删除了不必要的包，并加快了应用程序的部署和编排。


2. 语言堆栈容器镜像，其基础环境能用于 Python、Node.js、Ruby、.NET、ASP.Net、Java（基于 OpenJDK）、Go 和 Rust 等编程语言。


3. 应用程序堆栈容器镜像，能提供现成的容器化应用程序（如 RMT 和 PostgreSQL）。

### BCI 的优势是什么？

BIC 的主要优势包括：

- **可用性**：BCI 可用于 x86-64、arch64、s390x 和 ppc64le。

- **安全性**：容器镜像更安全，能减少容器漏洞扫描程序的通知数量。

### BCI 用例的是什么？

BCI 提供了一个稳定、安全和开放的生态系统，你可以在轻量灵活的环境中开发和部署应用程序，还能利用 SLES（SUSE Linux Enterprise Server）操作系统的稳定性和安全性。

另一方面，BCI 提供了以下机会：

- Rancher 用户：
   - 让 Rancher 能够使用稳定、可靠、安全和认证的企业组件进行构建。
   - 在对应用程序进行容器化时使用 SUSE 内部的操作系统知识。这是因为工具是相同的，不需要迁移路径（例如，由于 BCI 会作为容器基础，因此你可以将 Zypper 转为其它包管理器）。
- 开发者：
   - 如果你不想为云原生环境进行付费订阅，则可以选择免费的 BCI。
   - BCI 可以部署在任何操作系统中，能帮助你在多云厂商生态系统内迁移并避免云厂商锁定。
- ISV（Independent Software Vendor）：
   - 使用稳定、可靠、安全且经过认证的企业级操作系统来容器化应用程序。
   - 使用免费的 Linux 来构建应用程序，无需在链中提供支持和安全服务。
   - 容器化时进行导航（软件、工具、文档、咨询）。
   - 在各种主机上运行应用程序。

### BCI 中提供了哪些软件包和库？

SUSE 提供了多种 BCI，开发人员可以随时选择符合需求的 BCI。同时，开发人员可以使用知名的工具和库，如编译器、加密库以及多种操作系统工具等，如下：

- 包管理器和工具，如 Zypper、RPM、sysctl 或 glibc。
- 库，如 lib-acl、lib-crypto、lib-openssl、libldap。

### 在 BCI 上构建产品需要哪些法律协议？

你需要接受 SUSE Enterprise Linux 默认和标准的条款和条件。

## Why?

### SUSE 为什么要推出 BCI？

我们希望为开发人员和集成商提供真正开放、灵活和安全的容器镜像和应用程序开发工具，避免用户受替代产品的限制。

为了满足受监管市场的需求，SUSE 计划提供经过专门强化和认证的 SLE 解决方案。

### BCI 支持哪些硬件平台？（x86_64、AARM64、POWER、IBM zSeries）？

BCI 在 x86_64、aarch64、ppcle64 和 s390x 上可用（.NET 镜像现在仅在 x86-64 上可用）。

## How?

### 是否需要订阅才能使用 BCI？

不需要，你无需订阅即可使用 BCI。

### 我是否需要 SUSE Linux 环境来构建基于 BCI 的镜像？

不需要，你可以在任何支持基于 OCI 镜像进行构建的环境中构建和运行 BCI。

### 部署 BCI 是否需要 SUSE Linux 环境？

不需要，你可以在任何经过​​认证的 Kubernetes Deployment 或任何与 OCI 兼容的运行时中运行 BCI。

### 我可以自由分发基于 BCI 构建的应用程序吗？

基于 BCI 重新分发应用程序是没有限制的，因此你可以通过 EULA 自由复制、使用、分发以及重新分发镜像。

### 我可以在不使用 SUSE 镜像仓库的情况下分发基于 BCI 的容器镜像吗？

如果 BCI 镜像可以免费生成、使用和分发，你可以使用任何镜像仓库来分发基于 BCI 的应用程序。

因此，是的，你可以根据需要分发基于 BCI 的应用程序。

### 我是否可以将非 BCI RPM 添加到 BCI 镜像，并继续在 SUSE 之外的平台上重新分发生成的容器镜像？

由于在提供的镜像上添加的所有内容都会被视为应用程序或依赖项，因此你可以将非 BCI-RPM 添加到镜像中。

如果你遵守 EULA，SUSE 对重新分发没有任何限制。

### 是否推荐将 BCI 用于社区项目？

是的。

### BCI 会接收更新吗？

是的，我们通过 SUSE Linux Enterprise Server 仓库构建 BCI 镜像。我们为每个新的 SLE (SUSE Linux Enterprise) Service Pack 构建新的 BCI 镜像。

已发布 Service Pack 的 BCI 镜像会持续接收更新（例如安全更新）。

### 如何支持 BCI？

你可以在 [SUSE Lifecycle 仪表板](suse.com/lifecycle)中查看 BCI 的生命周期以及我们的其他产品。

### 基于 BCI 构建的应用程序是否受到支持？

SUSE 支持可用的 BCI 镜像。

通过容器镜像交付的应用程序需要由其厂商或开发人员提供支持。

### 什么是 BCI 生命周期？

通用 BCI（General Purpose BCI）遵循 SLE Service Pack 的通用支持生命周期。你可以在[此处](suse.com/lifecycle)查阅 SUSE Linux Enterprise Server 的生命周期。

应用程序和语言堆栈 BCI 的生命周期限制于对应的应用程序或语言堆栈，不与对应的 Service Pack 关联。有关详细信息，请参阅 [SUSE 生命周期](suse.com/lifecycle)。  
长期服务包支持（Long Term Service Pack Support，LTSS）不支持 BCI 镜像。

### 如何在 BCI 中请求新功能？

SUSE 内部：通过 https://jira.suse.com/projects/PM 请求新功能，并创建一个新的 PM Pool Epic。

### BCI 是否支持将容器镜像分发到任何位置？

是的，SUSE 不会监督你处理及分发镜像的方式。你可以自由分发 BCI，如果你遵守 EULA，你也可以根据需要分发应用程序。

### 如果 BCI 中缺少某些内容，我可以添加非 BCI 的包吗？

是的。但是由于 BCI 来自我们的镜像仓库，因此 SUSE 支持 BCI。将包添加到 BCI 是开发过程的一部分，但 SUSE 不直接支持此操作。
