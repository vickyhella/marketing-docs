# 使用 Rancher 进行多云部署

[Rancher](https://www.rancher.com/) 是一个 [Kubernetes](https://kubernetes.io/) 管理平台，Rancher 为多云上的容器操作提供了一致的环境。它解决了多云 Kubernetes 部署的挑战，例如查看工作负载运行位置以及集中进行身份认证和访问控制。

多云通过跨云提供商分发应用程序来提高弹性。你也能利用各朵云的优点来提高自身竞争力。此外，多云还降低了云厂商锁定的可能性，让你避免过于依赖某个云厂商。

虽然多云的优势很多，但是管理多云 Kubernetes 的困难还是让人望而却步。部署多个集群，将它们作为一个单元使用并监控整个集群是非常艰巨的任务。你需要一种方法来持续实现授权、可观测性和安全的最佳实践。

在本文中，你将了解 Rancher 如何解决这些问题，让你轻松在多云场景中使用 Kubernetes。

## Rancher 和多云部署

Rancher 的优势之一是让你在使用多个环境时获得一致的体验。无论集群位于云端还是本地，你都可以管理所有集群的整个生命周期。它还抽象出 Kubernetes 实现之间的差异，创建了用于监控部署的统一界面。

![Diagram showing how Rancher works with all Kubernetes distributions and cloud platforms courtesy of James Walker](https://imgur.com/TrUHoHh.png)

Rancher 非常灵活，可以与新旧集群一起工作，并且支持通过以下三种方式连接集群：

1. **使用云 Kubernetes 服务配置新集群**：Rancher 可以为你创建新的 Amazon Elastic Kubernetes Service (EKS)、Azure Kubernetes Service (AKS) 和 Google Kubernetes Engine (GKE) 集群。该过程在 Rancher UI 中完全自动化进行。此外，你还可以导入现有集群。
2. **在独立的云基础设施上配置新集群**：Rancher 可以通过在你的云提供商上配置新的计算节点来[部署 RKE、RKE2 或 K3s](https://rancher.com/docs/rke/latest/en/installation) 集群。此选项支持 Amazon Elastic Compute Cloud (EC2)、Microsoft Azure、[DigitalOcean](https://www.digitalocean.com/)、[Harvester](https://www.suse.com/products/harvester)、[Linode](https://www.linode.com/) 和 [VMware vSphere](https://www.vmware.com/products/vsphere.html).
3. **使用你自己的集群**：支持手动连接在本地或其他云环境中运行的 Kubernetes 集群。这样，你可以在混合部署的情况下结合使用本地和公有云基础设施的多项功能。

![Screenshot of adding a cluster in Rancher](https://imgur.com/dtYcUee.png)

添加了多云集群后，你就可以使用 Rancher 无缝管理这些集群。

### 统一的仪表板

多云最大的问题之一是跟踪部署的内容、位置以及运行情况。Rancher 为你提供了统一的仪表板，显示了你的每个集群，包括集群的云环境及其资源利用率：

![Screenshot of the Rancher dashboard showing multiple clusters](https://imgur.com/QUmIMcm.png)

![Clusters screenshot](https://imgur.com/4vbOmUd.png)

Rancher 主页面集中显示了你已注册的集群，涵盖了你的云上和本地部署。侧边栏也集成了集群的快捷方式，你可以在环境之间快速切换。

导航到特定集群后，你可以在 **Cluster Dashboard** 页面查看容量、利用率、事件和部署的概览视图：

![Screenshot of Rancher's **Cluster Dashboard**](https://imgur.com/rikVq5m.png)

继续向下滚动，你可以查看用于分析性能的集群指标：

![Screenshot of viewing cluster metrics in Rancher](https://imgur.com/VH4jJww.png)

有了 Rancher，你可以在单个工具中访问所有 Kubernetes 环境的重要监控数据，不再需要单独登录各个云提供商的控制面板。

### 集中的授权和访问控制

Kubernetes 内置了对 [RBAC](https://kubernetes.io/docs/reference/access-authn-authz/rbac) 的支持，用来限制用户可以执行的操作。但是，这对于多云部署来说是不够的，因为你必须在每个集群中单独管理和维护策略。

Rancher 通过添加集中式身份认证系统提高了多云 Kubernetes 的可用性。你可以在 Rancher 中设置用户账号或[使用 LDAP、SAML 和 OAuth 等协议](https://ranchermanager.docs.rancher.com/pages-for-subheaders/authentication-config)连接外部服务。

创建用户后，你可以为用户分配特定的访问控制规则，限制用户在 Rancher 和你的集群中的权限。[全局权限](https://ranchermanager.docs.rancher.com/how-to-guides/new-user-guides/authentication-permissions-and-global-configuration/manage-role-based-access-control-rbac/global-permissions)定义了用户如何管理你的 Rancher。例如，你可以创建和修改集群连接，而[集群和项目级别角色](https://ranchermanager.docs.rancher.com/how-to-guides/new-user-guides/authentication-permissions-and-global-configuration/manage-role-based-access-control-rbac/cluster-and-project-roles)用于在选择集群后配置可用的操作。

要创建新用户，单击左上角的菜单图标来展开侧边栏，然后选择 **Users & Authentication**。然后，在显示现有用户的页面上单击 **Create** 按钮：

![Screenshot of the Rancher UI](https://imgur.com/AdUvtC6.png)

在以下页面填写新用户的凭证：

![Screenshot of creating a new user in Rancher](https://imgur.com/1SakdSI.png)

向下滚动页面，然后为新用户分配权限。

设置用户的全局权限，控制他们在 Rancher 中的整体访问级别。然后，在下方为角色的特定操作添加粒度更细的策略。完成后，点击右下角的 **Create** 按钮添加账号。至此，用户可以登录 Rancher：

![Screenshot of assigning a user's global roles in Rancher](https://imgur.com/HlK6J0C.png)

接下来，导航到某个集群，然后前往侧边栏中的 **Cluster > Cluster Members**。单击右上角的 **Add** 按钮来授予用户对集群的访问权限：

![Screenshot of adding a cluster member in Rancher](https://imgur.com/WMtPJoJ.png)

使用下方页面搜索用户账号，然后设置用户在集群中的角色。单击右下角的 **Create** 后，用户将能够执行你分配的集群交互操作：

![Screenshot of setting a cluster member's permissions in Rancher](https://imgur.com/496qhLo.png)

#### 添加集群角色

要更精确地控制访问，你可以设置基于 Kubernetes RBAC 的角色。它们可以应用于全局（Rancher）、特定集群或项目/命名空间。三者的创建方法类似。

要创建集群角色，再次展开 Rancher 侧边栏并返回到 **Users & Authentication** 页面。从左侧菜单中选择 **Roles**，选择 **Cluster** 选项卡。然后，单击右上角的 **Create Cluster Role** 按钮：

![Screenshot of Rancher's Cluster Roles interface](https://imgur.com/raxnCID.png)

为角色命名并输入描述（可选）。接下来，使用 **Grant Resources** 来定义角色包含的 Kubernetes 权限。此示例允许用户在集群中创建和列出 Pod。单击 **Create** 按钮添加角色：

![Screenshot of defining a cluster role's permissions in Rancher](https://imgur.com/E2SQ20h.png)

向集群添加新成员后，该角色会显示：

![Screenshot of selecting a custom cluster role for a cluster member in Rancher](https://imgur.com/OYPVb07.png)

### Rancher 和多云安全

Rancher 通过提供主动机制来增强多云安全。除了集中身份认证和 RBAC 的优势外，Rancher 还集成了[其他安全措施](https://ranchermanager.docs.rancher.com/pages-for-subheaders/rancher-security)来保护你的集群和云环境。

Rancher 在[互联网安全中心 (CIS) Benchmark](https://www.cisecurity.org/benchmark/kubernetes) 基准的基础上维护了一份[综合加固指南](https://ranchermanager.docs.rancher.com/pages-for-subheaders/rancher-v2.7-hardening-guides)，可帮助你实施最佳实践并识别漏洞。你可以在 Rancher 应用程序中根据 Benchmark 扫描集群。

为此，导航到你的集群，然后展开左侧栏中的 **Apps > Charts**。从列表中选择 **CIS Benchmark** Chart：

![Screenshot of the CIS Benchmark app in Rancher's app list](https://imgur.com/GBimemG.png)

单击下一个页面上的 **Install** 按钮：

![Screenshot of the CIS Benchmark app's details page in Rancher](https://imgur.com/KW7mfzf.png)

按照以下步骤完成安装：

![Screenshot of the CIS Benchmark app's installation screen in Rancher](https://imgur.com/Opwmhhv.png)

该过程可能需要几分钟，完成后你会在日志窗格中看到 “SUCCESS” 消息：

![Screenshot of the CIS Benchmark app's installation logs in Rancher](https://imgur.com/Wpj4tc5.png)

现在，导航回你的集群。你会在 Rancher 的侧边栏中找到一个新的 CIS Benchmark 项目。展开此菜单并单击 **Scan**，然后在出现的页面上单击 **Create** 按钮：

![Screenshot of the CIS Benchmark interface in Rancher](https://imgur.com/vyYbvx8.png)

在下一个页面上，系统会提示你选择扫描配置文件，该文件定义了要执行的加固检查。你可以更改默认值来选择不同的 Benchmark 测试或 Kubernetes 版本。按 **Create** 按钮开始扫描：

![Screenshot of creating a CIS Benchmark scan in Rancher](https://imgur.com/Lt078pA.png)

然后，扫描将显示在 **CIS Benchmark > Scan** 页面上的 **Scans** 列表中：

![Screenshot of the CIS Benchmark **Scans** interface in Rancher, with a running scan displayed](https://imgur.com/6tpep9o.png)

完成后，你可以选择表中的扫描并在浏览器中查看结果：

![Screenshot of viewing CIS Benchmark scan results in the Rancher UI](https://imgur.com/LWXfpJr.png)

## Rancher 帮助 DevOps 团队扩展多云环境

多云是非常困难的，更多的资源通常意味着更高的开销、更大的攻击面和快速膨胀的工具链。这些问题可能会成为你扩展的阻碍。

Rancher 结合了多个独特功能，可帮助运维人员有效地处理分布在多个环境中的部署。

### 自动备份集群，安全更有保证

Rancher 具有一个[备份系统](https://ranchermanager.docs.rancher.com/pages-for-subheaders/backup-restore-and-disaster-recovery)，你可以将其作为 Operator 安装到集群中。此 Operator 将备份你的 Kubernetes API 资源，你可以轻松实现灾难恢复。

要添加 Operator，导航到集群并从侧边栏中选择 **Apps > Charts**。然后找到 **Rancher Backups** 应用并按照提示进行安装：

![Screenshot of the Rancher Backups app description in the Rancher interface](https://imgur.com/nKs18kN.png)

然后，**Rancher Backups** 会出现在导航菜单中。单击 **Create** 按钮定义一个一次性或定期备份计划：

![Screenshot of the **Backups** interface in Rancher](https://imgur.com/tpnuPqy.png)

配置你的备份详情：

![Screenshot of configuring a backup in Rancher](https://imgur.com/lnws70F.png)

创建备份后，如果数据被意外删除或发生灾难，你可以[进行恢复](https://ranchermanager.docs.rancher.com/how-to-guides/new-user-guides/backup-restore-and-disaster-recovery/restore-rancher)。使用 Rancher，你可以通过统一的流程为所有集群创建备份，从而提高环境的复原能力。

### Rancher 与多云解决方案集成

Rancher 是用于在任何集群中管理 Kubernetes 的统一平台，这也是 Rancher 的一大优势。与其他生态系统工具结合使用时，Rancher 的表现还能更加出色。Rancher 集成了以下组件，为特定用例提供了更聚焦的支持：

- **[Longhorn](https://www.suse.com/products/longhorn)**：分布式云原生块存储，可在任何地方运行并支持自动配置、安全和备份。你可以在 Rancher UI 中将 Longhorn 部署到你的集群，以便为工作负载提供更可靠的存储。
- **[Harvester](https://www.rancher.com/products/harvester)**：裸机服务器上的超融合基础架构（HCI）解决方案。它提供了一个虚拟机管理系统，补充了 Rancher 的 Kubernetes 集群功能。通过同时使用 Harvester 和 Rancher，你可以高效地管理本地集群以及托管这些集群的基础设施。
- **[Helm](https://helm.sh/)**：Kubernetes 应用程序的标准包管理器。它将应用程序的 Kubernetes 清单打包到一个称为 Chart 的集合中，你可以随时使用单个命令进行部署。Rancher 原生支持 Helm Chart，并且提供了一个方便的界面，你可以通过其应用程序系统将 Chart 部署到集群中。

结合使用 Rancher 与其他常用工具后，你可以让你的多云 Kubernetes 变得更加强大。有了自动化存储、本地基础设施管理和打包好的应用程序，你能随心所欲地进行扩展，无需手动配置环境和创建应用程序资源。

### 使用 Rancher Fleet 部署到大规模环境

Rancher 还可以帮助你使用自动化 GitOps 部署应用程序。[Rancher Fleet](https://fleet.rancher.io/) 是专门用于容器化工作负载的 GitOps 解决方案，它提供了透明的可见性和灵活的控制，并支持在多个环境中进行大规模部署。

Rancher Fleet 为你管理 Kubernetes 清单、Helm Chart 和 [Kustomize](https://kustomize.io/) 模板，并将它们转换为可以自动部署在集群中的 Helm Chart。要在 Rancher 安装中设置 Fleet，单击左上角的菜单图标，然后从滑出式主菜单中选择 **Continuous Delivery**：

![Screenshot of the **Rancher Fleet** landing screen](https://imgur.com/QpHQAPd.png)

单击 **Get started** 连接你的第一个 Git 仓库并将其部署到集群中。Rancher 支持在所有环境中使用标准化的交付工作流。你不再局限于单一的云提供商、交付渠道或 PaaS：

![Screenshot of creating a new Rancher Fleet Git repository connection](https://imgur.com/ywtih5n.png)

## 结论

多云为更灵活、更高效的部署提供了新的机遇。通过结合多个云提供商的解决方案，你可以为每个组件选择最佳选项，同时避免供应商锁定的风险。

但是，将多云与容器和 Kubernetes 结合使用的企业还是经常会遇到运维挑战。管理位于多个不同环境（例如公有云和本地服务器）中的集群通常困难重重。此外，自行实现集中监控、访问控制和安全策略是一项非常繁重的工作。

[Rancher](https://rancher.com/) 提供了用于配置基础设施、安装 Kubernetes 和管理部署的统一平台，助你轻松应对这些挑战。它[兼容 Google GKE、Amazon EKS、Azure AKS 以及你自己的集群](https://www.suse.com/suse-rancher/support-matrix/all-supported-versions/rancher-v2-7-0/)，是实现多云 Kubernetes 互操作的终极解决方案。[立即试用 Rancher](https://ranchermanager.docs.rancher.com/getting-started/overview) 来配置和扩展多云 Kubernetes 吧！