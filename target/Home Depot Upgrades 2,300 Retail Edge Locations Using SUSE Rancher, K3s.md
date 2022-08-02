# Home Depot 使用 SUSE Rancher 和 K3s 升级 2,300 个零售边缘位置

SUSECon 2022 是 SUSE 举办的为期两天的活动，[SUSECon](https://susecon.com/) 本次也是通过线上的形式举办。SUSE 是“三大”企业 Linux 公司之一，另外两家分别是 Red Hat 和 Canonical。

与去年一样，SUSECon 2022 主要关注的是云原生技术和 Edge。SUSE 在 2020 年花费了超过 [6 亿美元收购 Rancher Labs](https://www.datacenterknowledge.com/open-source/rancher-acquisition-may-make-suse-kubernetes-and-hybrid-cloud-powerhouse)，Rancher 当时是一家在 Cupertino 成立 6 年的初创公司，围绕其广受欢迎的 Rancher Kubernetes 管理平台构建。

收购后，Rancher 和 SUSE 的工程师一直专注于将 Rancher 产品与 SUSE 的同名 Linux 发行版进行紧密集成，希望能帮助公司更有效地与 Red Hat Enterprise Linux 及其以 OpenShift 平台竞争。

这场花费 6 亿美元的收购似乎物有所值。在今年的 SUSECon 上，Deutsche Telecom、Hewlett Packard Enterprise、UberCloud 和 Fujitsu 等多家企业均对 SUSE/Rancher 这个组合赞不绝口。

与 SUSE 的 Edge 总经理 Keith Basil 进行主题访谈的还有 Home Depot 系统工程总监 Zachary Hardin，Home Depot 最近将所有 2,300 多个零售店都转到了基于 Rancher 的新架构中。

![Home Depot](https://www.datacenterknowledge.com/sites/datacenterknowledge.com/files/styles/article_featured_standard/public/home-depot_0.jpg?itok=iFTtoVkc)

Basil 最近告诉 Data Center Knowledge，Home Depot 的主要需求是避免手动维护容器化应用程序的可用性。

Basil 告诉 Data Center Knowledge：“Home Depot 面临的挑战在于它们是一家云原生商店，它们的店内应用程序[一直是容器化的](https://www.datacenterknowledge.com/edge-computing/how-kubernetes-could-underpin-edge-computing-platforms)。他们非常欣赏 K3s 的简易性，你只需要运行单个二进制文件就可以随时拥有一个经 CNCF 认证的 Kubernetes 发行版”。

## **K3s 和 Rancher**

K3s 是 Kubernetes 的缩小版本，用于管理 Rancher 专门为边缘位置（例如 Home Depot 的零售店）设计的容器。这些边缘位置具备的资源可能比较有限，且专业的 IT 人员可能比较稀缺。整个 K3s 二进制文件不超过 50MB，并且只需要 512MB 即可运行（而标准 Kubernetes 部署的平均最低的 RAM 要求是 4GB），因此它非常容易在单节点集群中使用。

对于没有足够的专业知识的人来说，etcd 数据存储设置非常困难，而 K3s 不要求使用 etcd 数据存储，因此用户可以使用熟悉（且相对简单）的 SQL 数据库，如 MySQL 或 PostgreSQL，甚至可以使用 K3s 的嵌入式 SQLite 数据库。用户还可以使用其他数据存储方式（例如基于嵌入式 etcd 构建的 K3s 嵌入式 HA 数据存储），对于缺乏管理数据库的运营费用的边缘位置而言，这是一个高度可用的解决方案。

边缘位置适合使用 K3s 的另一个更重要的原因是 K3s 的易用性。

Basil 表示，如果同时使用 K3s 以及 Rancher 和 Fleet（Rancher 的 GitOps 解决方案），操作甚至可以更加简单。

他表示，“你可以在操作系统上使用 K3s。在集群感知后，该集群将与 Rancher 通信，然后你可以开始将应用程序部署到这些集群，然后再部署到远程零售店。如果你的 Git 仓库中具有要在各个零售店中运行的应用程序的 manifest，你可以将集群指向该 Git 仓库，然后它会拉取该零售店所需的所有内容”。

## **其他优势**

将 K3s 或其他 Kubernetes 发行版与 Rancher 一起用于边缘部署（例如 Home Depot 的数千个边缘位置部署）的另一个优势，是数据中心不再需要专业的 IT 人员进行维护。

Basil 指出，通常情况下，每个远程位置至少有一个三节点集群，换言之，每个零售店至少具有三台独立的主机（就 Home Depot 而言，则是超过 6,900 台主机），而每台主机都需要使用最新的安全补丁、进行操作系统升级等。

他表示，“之前，我们需要重复使用 Kubernetes 参数、方法和最佳实践来更换运行中的集群的操作系统，升级操作系统后，我们要将主机带回集群，然后继续处理下一台主机。现在没有人能这样做了，因此 Rancher 就成为了整个业务流程的协调程序”。

Rancher 不仅可以在没有现场人员的情况下更换容器基础设施的操作系统，还可以轻松让 Kubernetes 保持最新状态。

“如果要从 Kubernetes 1.23 升级到 1.24，你必须在 2,300 个位置中重复执行操作。有了 Rancher，我们则能通过 Fleet 的 GitOps 轻松实现这一点，并且还可以处理此类下游集群升级的规模”。

Basil 表示，虽然目前 SUSE 的所有竞争对手都无法在规模上与 Rancher 媲美，但现在还不是我们自满的时候，竞争对手已经盯上了我们。
