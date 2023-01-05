# 如何选用 K3s 和 RKE2

[K3s](https://k3s.io/) 和 [Rancher Kubernetes Engine (RKE2)](https://docs.rke2.io/) 是 [SUSE Rancher 容器平台](https://www.rancher.com/)的两个 [Kubernetes](https://kubernetes.io/) 发行版。这两个项目都可以用来运行生产就绪的集群，但是它们适用的用例不同，两者都有独特的功能。

本文将介绍这两个项目的相同点和差异。通过本文你将了解如何合理选用 RKE2 和 K3s，来满足容器化工作负载的安全性和合规性。

## K3s 和 RKE2

K3s 仅使用一个不到 70MB 的二进制文件来提供生产就绪的 Kubernetes 集群。由于 K3s 非常轻巧，因此它非常适合在边缘 IoT 设备、低功耗服务器和开发工作站上运行 Kubernetes。

RKE2 也能运行生产就绪的集群。它与 K3s 一样简单易用，而且注重安全性和合规性。

RKE2 是从 RKE 项目演变而来的。RKE 也称为 RKE Government，这个名称也反映了它适用于要求最苛刻的行业。但是，它的应用范围不仅限于政府机构，该发行版是所有重视安全性和合规性的组织的理想选择，因此我们将其发展成了现在的 RKE2。

## K3s 和 RKE2 的相似之处

K3s 和 RKE2 都受 Rancher 完全支持，都是云原生计算基金会 (CNCF) 认证的 Kubernetes 发行版。虽然二者的目标用例不同，但这它们的启动和操作方式类似，都可以通过 [Rancher Manager](https://docs.ranchermanager.rancher.io/) 部署，而且都能使用行业标准的 [containerd 运行时](https://containerd.io/)运行容器。

### 可用性

K3s 和 RKE2 的安装非常方便，而且启动速度都非常快。

你只需使用一条命令并等待大约 30 秒，就可以在新主机上启动 K3s 集群：

    $ curl -sfL https://get.k3s.io | sudo sh -

该服务已注册并启动，因此你可以立即运行 kubectl 命令与集群进行交互：

    $ k3s kubectl get pods

RKE2 也类似。你可以使用一个简单的安装脚本来下载其二进制文件：

    $ curl -sfL https://get.rke2.io | sudo sh -

RKE2 默认不启动服务。你可以运行以下命令，从而在 server（control plane）模式下启动 RKE2：

    $ sudo systemctl enable rke2-server.service
    $ sudo systemctl start rke2-server.service

你可以在 `/var/lib/rancher/rke2/bin` 中找到附带的 kubectl 二进制文件。默认情况下，它不会添加到你的 `PATH` 中，kubeconfig 文件会存放到 `/etc/rancher/rke2/rke2.yaml`：

    $ export KUBECONFIG=/etc/rancher/rke2/rke2.yaml
    $ /var/lib/rancher/rke2/bin/kubectl get pods

### 易用性

除了可用性，K3s 和 RKE2 还非常易用。你可以通过在每个节点上重复运行安装脚本来升级集群：

    # K3s
    $ curl -sfL https://get.k3s.io | sh -

    # RKE2
    $ curl -sfL https://get.rke2.io | sh -

你需要重复提供原始安装命令中的标志。

你可以使用 Rancher 的 [system-upgrade-controller](https://github.com/rancher/system-upgrade-controller) 来[支持](https://docs.k3s.io/upgrades/automated)自动升级。安装 controller 后，你可以通过声明[创建 Plan 对象](https://www.suse.com/c/rancher_blog/upgrade-a-k3s-kubernetes-cluster-with-system-upgrade-controller)，该对象描述了如何将集群迁移到新版本。Plan 是由 controller 提供的[自定义资源定义（CRD）](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources)。

备份和恢复数据是另一个常见的 Kubernetes 挑战。K3s 和 RKE2 在这方面也非常类似。快照会自动写入并保留一段可配置的时间。你可以运行以下命令轻松地通过快照恢复集群：

    # K3s
    $ k3s server \
        --cluster-reset \
        --cluster-reset-restore-path=/var/lib/rancher/k3s/server/db/etcd-old-<BACKUP_DATE>

    # RKE2
    $ rke2 server \
        --cluster-reset \
        --cluster-reset-restore-path=/var/lib/rancher/rke2/server/db/etcd-old-<BACKUP_DATE>

### 部署模型

K3s 和 RKE2 的单节点部署模型相同。他们将所有依赖项捆绑到一次下载中，因此你不需要具备很多 Kubernetes 经验就能部署一个正常运行的集群。

它们还支持[离线环境](https://docs.rke2.io/install/airgap)，因此它们都能安装在与物理网络隔离的主机中。我们在 Release artifacts 中提供了离线镜像，将镜像传输到主机后，运行安装脚本将引导你的集群。

### 高可用性和多节点

K3s 和 RKE2 都是为生产环境运行而设计的。开发中也经常使用 K3s，K3s 被誉为理想的单节点集群。它具有强大的[多节点管理](https://docs.k3s.io/installation/requirements)功能，能支持物联网设备群。

两个项目都可以运行[高可用](https://docs.k3s.io/installation/ha)的 control plane。你可以将 control plane 组件的副本分布在多个 Server 节点上，并使用外部数据存储而不是嵌入式数据存储。

## K3s 和 RKE2 的差异

K3s 和 RKE2 都使用单个二进制 Kubernetes，支持高可用设置而且易于备份，两者的许多命令都可以互换。但是，一些关键的差异会影响它们的使用场景，这也是我们把这两个发行版区分为两个独立项目的原因。

### RKE2 更接近上游 Kubernetes

K3s 已通过 CNCF 认证，但 K3s 在某些方面还是与上游 Kubernetes 不同的。虽然嵌入式 etcd 实例是现代版本中的[可用选项](https://docs.k3s.io/installation/datastore)，但是 K3s 使用 [SQLite](https://www.sqlite.org/index.html) 而不是 [etcd](https://etcd.io/) 作为默认数据存储。K3s 还附带了其他实用程序，例如 [Traefik Ingress controller](https://docs.k3s.io/networking#traefik-ingress-controller)。

RKE2 更接近标准 Kubernetes，[提升一致性](https://docs.rke2.io/)是其主要特性之一。因此，你针对其他发行版开发的工作负载也能可靠地在 RKE2 中运行。它降低了当 K3s 与上游 Kubernetes 不一致时可能发生的风险。RKE2 自动使用 etcd 进行数据存储，并去掉了 K3s 中包含的非标准组件。

### RKE2 使用嵌入式 etcd

K3s 中的标准 SQLite 数据库更紧凑，可以在小型集群中优化性能。相比之下，RKE2 默认使用 etcd，因此更一致，让你能直接集成其他需要 etcd 数据存储的 Kubernetes 工具。

虽然 K3s 可以配置 etcd，但你需要手动打开该选项。RKE2 是围绕它设计的，因此能降低配置错误和性能不佳的风险。

K3s 还支持使用 MySQL 和 PostgreSQL 作为替代的存储解决方案，因此你可以使用现有的关系数据库工具来管理 Kubernetes 数据，例如进行备份和维护。RKE2 仅适用于 etcd，不支持基于 SQL 的存储。

### RKE2 更注重安全

RKE2 更加注重安全性。边缘操作是 K3s 的专长，而 RKE2 最大的优势是安全性。

#### 针对 CIS Benchmark 的强化

RKE2 发行版的[默认配置](https://docs.rke2.io/security/hardening_guide)兼容 CIS Kubernetes Hardening Benchmark v1.23（RKE2 v1.25 及更早版本中为 v1.6）。RKE2 的默认值能让你的集群以最少的手动操作达到标准要求。

你仍然需要加强节点上的操作系统级别控制，其中包括应用适当的[内核参数](https://docs.rke2.io/security/hardening_guide/#setting-up-hosts)并确保 [etcd 数据目录](https://docs.rke2.io/security/hardening_guide/#ensure-etcd-is-configured-properly)受到保护。

在启动 RKE2 时，你可以通过将 `profile` 标志设置为 `cis-1.23` 来强制使用安全配置。如果操作系统没有得到适当的强化，RKE2 将退出并提示错误。

除了配置操作系统之外，你还必须设置合适的[网络策略](https://kubernetes.io/docs/concepts/services-networking/network-policies)和 [Pod 安全准入规则](https://kubernetes.io/docs/concepts/security/pod-security-admission/)，从而保护集群的工作负载。你可以将安全准入控制器配置为使用[符合 CIS Benchmark 的配置文件](https://docs.rke2.io/security/hardening_guide)，这将防止不合规的 Pod 部署到你的集群。

#### 定期扫描威胁

RKE2 发行版的安全性会在构建流水线时进行维护。我们会使用 [Trivy](https://github.com/aquasecurity/trivy) 容器漏洞工具[定期扫描](https://docs.rke2.io/)组件以查找新的通用漏洞披露 (CVE)。因此，你可以认为 RKE2 本身不存在可能让攻击者进入环境的威胁。

#### 符合 FIPS 140-2 标准

K3s 缺乏正式的安全认证，而 RKE2 [符合 FIPS 140-2 标准](https://docs.rke2.io/security/fips_support/)。该项目的 [Go 代码](https://docs.rke2.io/security/fips_support)是使用 FIPS 验证的加密模块而不是 Go 标准库中的版本编译的。该发行版的每个组件（Kubernetes API Server、kubelet、捆绑的 kubectl 二进制文件等）都是使用 FIPS 兼容的编译器编译的。

符合 FIPS 意味着 RKE2 可以部署在政府环境以及其他要求验证加密性能的环境中。如果你使用内置组件（例如 containerd 运行时和 etcd 数据存储），整个 RKE2 堆栈都是合规的。

## 什么时候使用 K3s

如果你需要运行在边缘的高性能 Kubernetes 发行版，K3s 则是你的首选解决方案。K3s 也非常适合单节点开发集群以及 CI 流水线和其他构建工具中使用的临时环境。

如果你的主要目标是使用单个二进制文件部署具有所有依赖项的 Kubernetes，那么该发行版则是你的最佳选择。它非常轻量、启动快速且易于管理，因此你可以专注于编写和测试应用程序。

## 什么时候使用 RKE2

如果你非常关注安全性，例如需要在政府服务和其他受到高度监管的行业（包括金融和医疗保健）中使用，你就适合选用 RKE2。如前所述，完整的 RKE2 发行版符合 FIPS 140-2 标准，并通过 CIS Kubernetes Benchmark 进行了强化。它也是[唯一获得 DISA STIG 认证](https://public.cyber.mil/announcement/disa-releases-the-rancher-government-solutions-rke2-security-technical-implementation-guide)的 Kubernetes 发行版。

RKE2 已经过全面认证并与上游 Kubernetes 紧密结合。它去掉了非标准 Kubernetes 或不稳定的 alpha 功能的 K3s 组件，因此更容易在不同环境中相互操作你的部署。它还可以降低在手动强化 Kubernetes 集群时因疏忽而发生的不合规风险。

选择 RKE2 而不是 K3s 的另一个主要用例是近边缘计算。RKE2 支持[多个 CNI 网络插件](https://rancher.com/docs/rancher/v2.6/en/cluster-admin/editing-clusters/rke2-config-reference)，包括 Cilium、Calico 和 Multus。[Multus](https://github.com/k8snetworkplumbingwg/multus-cni) 允许 pod 连接多个网络接口，因此非常适合 [telco](https://www.telco.com/) 配送中心以及拥有多个不同生产设施的工厂等用例。在这些情况下，使用不同的网络适配器提供强大的网络支持至关重要。K3s 附带了 Flannel 作为内置的 CNI 插件，你可以[安装不同的 CNI](https://docs.k3s.io/installation/network-options#custom-cni)，但所有配置都必须手动执行。RKE2 的默认发行版为常见的网络解决方案提供了集成选项。

## 结论

K3s 和 RKE2 都是流行的 Kubernetes 发行版，它们在多个方面相互重叠。它们的部署简单、维护便捷，而且性能和兼容性都很高。

虽然专为[微型](https://www.suse.com/c/preparing-for-the-new-needs-of-edge-computing)和远边缘用例而设计，但 K3s 并不局限于这些场景。它还广泛用于开发、实验室或资源受限的环境中。但是，K3s 并不专注于安全性，因此你需要保护和强化你的集群。

RKE2 具有 K3s 的可用性，并将其应用于完全合规的发行版。它专注于安全性、与上游 Kubernetes 的关联，以及政府机构等受监管环境的合规性。由于 RKE2 内置了高级网络插件（包括 [Multus](https://github.com/k8snetworkplumbingwg/multus-cni)）支持，因此它适用于数据中心和近边缘用例。

你的选择取决于集群运行的位置以及你要部署的工作负载。如果你想为重视安全的工作负载使用强化的发行版，或者有 FIPS 140-2 合规要求，那么你可以选用 RKE2，它将帮助你建立和维护安全基线。如果你的工作负载对安全性的要求不高，或者是边缘工作负载，那么 K3s 则是一个不错的替代方案，能让你专注于应用程序本身而不是运行的环境。

这两个发行版都可以由 [Rancher](https://www.rancher.com/) 管理并与你的 DevOps 工具箱集成。你可以使用 [Fleet](https://www.suse.com/c/rancher_blog/fleet-management-for-kubernetes-is-here) 等解决方案通过 GitOps 策略大规模部署应用程序，然后前往 [Rancher 仪表板](https://www.suse.com/c/rancher_blog/rancher-desktop-now-includes-the-rancher-dashboard)监控你的工作负载。
