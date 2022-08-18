Kasten K10 by Veeam 与 SUSE Rancher
=============================================================

### *开始*

###### 摘要

本文档介绍和演示了 Kasten K10 by Veeam 与 SUSE Rancher 对企业云原生备份/恢复、灾难恢复和应用可移动性的应用。

**SUSE One Partner Solution Stack**

SUSE One Partner Solution Stack 是特色的 SUSE 解决方案，由 SUSE 和合作伙伴组件合作开发，旨在满足用户的实际需求。

**免责声明**：SUSE 技术参考文档（SUSE Technical Reference Documentation）由 SUSE 员工和第三方自愿贡献，旨在提供执行特定操作的示例。这些文档非常注重细节，但是，我们也不能保证描述是完全准确的。SUSE 无法验证这些文档中描述的操作是否符合声明的要求，或者这些操作是否具有意外后果。SUSE LLC、其附属机构、作者和翻译人员不对可能的错误或其后果负责。

**作者**：Adam Bergh, Solutions Architect, Cloud Native Technical Partnerships, Kasten by VeeamAuthor: Terry Smith, Global Partner Solutions Director, SUSE

**作者**：Gerson Guevara, IHV Solutions Architect, SUSE

**出版日期**：2022-07-27

## 1 简介
-------------------------------------------------------------------------------------------------------------------------------------------

### 1.1 用途

现在，企业越来越向云原生转移（例如使用容器化工作负载和 SUSE Rancher 等 Kubernetes 管理平台）。企业的目标是提高灵活性、规模和弹性，以便加速创新并快速适应动态条件。在这种永远在线的 IT 环境中，应用程序的移动性和数据保护就十分关键。

[![logo lockup suse rancher veeam kasten hor dark new](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/logo-lockup_suse-rancher_veeam-kasten_hor_dark_new.svg)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/logo-lockup_suse-rancher_veeam-kasten_hor_dark_new.svg)

Kasten K10 by Veeam® 数据管理平台为企业运营团队提供了一个易于使用、可扩展且安全的系统，用于云原生应用程序的备份和恢复、灾难恢复和移动。

### 1.2 范围

本指南概述了在 SUSE Rancher Kubernetes 环境中安装和设置 Kasten K10 by Veeam，以及执行应用程序的简单备份和恢复的步骤。

### 1.3 受众

本文档适用于 IT 运营团队、备份管理员、DevOps 和 DevSecOps 团队，以及负责云原生环境的业务连续性、灾难恢复、勒索软件和威胁控制以及应用程序迁移的其他人员。

## 2 技术概述
-------------------------------------------------------------------------------------------------------------------------------------------------------

Kasten K10 by Veeam® 数据管理平台与 SUSE Rancher 深度集成，并提供了跨 Kubernetes 发行版和云平台的生态系统支持，这为企业运营团队提供了灵活的部署环境选项（本地、公共云和混合云）。K10 是由策略驱动并可扩展的。它提供了很多企业功能，例如全谱一致性、数据库集成、自动应用程序发现、多云移动性和强大的 Web UI。

[![rancher veeam kasten architecture 1](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_architecture-1.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_architecture-1.png)

## 3 先决条件
---------------------------------------------------------------------------------------------------------------------------------------------

本指南涉及以下内容：

- SUSE Rancher

   本指南使用的是 Rancher 2.6。

   有关详细信息，请参阅 [SUSE Rancher 安装指南](https://rancher.com/docs/rancher/v2.6/en/installation/requirements/)。
- 由 SUSE Rancher 管理的 Kubernetes 集群。你可以使用任何 CNCF 认证的 Kubernetes 集群。

   请参阅 [SUSE Rancher 支持矩阵](https://www.suse.com/suse-rancher/support-matrix/all-supported-versions/rancher-v2-6-3/)。

- 备份目标的存储

   外部备份存储目标（例如 NFS 文件服务器或云对象存储）。本文档将使用与 S3 兼容的外部对象存储桶。

- 用于演示备份和恢复功能的用户应用程序

   例如，你可以使用 [Helm Chart](https://bitnami.com/stack/wordpress/helm) 轻松安装 WordPress。

K10 可以安装在各种不同的环境中。为了确保安装顺利，你可以使用 primer 工具来执行几个实施前检查（pre-flight check）。该工具在集群的 pod 中运行，且会执行以下操作：

- 验证 Kubernetes 设置是否满足 K10 要求。
- 登记可用的 StorageClass。
- 如果存在 CSI provisioner，它还会对集群的 CSI 功能和相关对象进行基本验证。

   有关详细信息，请参阅文档中的 [CSI pre-flight 检查](https://docs.kasten.io/latest/install/storage.html#csi-preflight)。

运行以下命令部署 primer 工具：

```
curl https://docs.kasten.io/tools/k10_primer.sh | bash
```

> 注意：
> 这将创建并清理 ServiceAccount 和 ClusterRoleBinding，以便对 Kubernetes 集群执行健全性检查。

## 4 安装 Kasten K10
-------------------------------------------------------------------------------------------------------------------------------------------------------------

你可以在 SUSE Rancher 的 **Apps & Marketplace** 页面中轻松部署 Kasten K10。

1. 为 Kasten K10 应用程序创建一个新的[命名空间](https://rancher.com/docs/rancher/v2.6/en/project-admin/namespaces/)。


   1. 在 SUSE Rancher 的 UI 中，导航到 *Clusters* > *Project/Namespaces*：

      [![rancher veeam kasten step1 1](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_step1-1.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_step1-1.png)
   2. 为 Kasten K10 创建一个 “kasten-io” 命名空间：

      [![rancher veeam kasten k10 create namespace](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_create-namespace.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_create-namespace.png)


2. 安装 Kasten K10：


   1. 在 SUSE Rancher UI 中导航到 *Apps & Marketplace* > *Charts* 并搜索 “Kasten”：

      [![rancher veeam kasten k10 marketplace1](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_marketplace1.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_marketplace1.png)
   2. 选择 K10 Chart 并单击 *Install*：

      [![rancher veeam kasten k10 marketplace2](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_marketplace2.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_marketplace2.png)
   3. 在 **Namespace** 下拉框中选择 “kasten-io” 命名空间。你也可以选择 *Customize Helm options before install* 来自定义部署（非必选）。有关详细说明，请参阅 [Helm 选项完整列表](https://docs.kasten.io/latest/install/advanced.html#complete-list-of-k10-helm-options)。

      [![rancher veeam kasten k10 marketplace3](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_marketplace3.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_marketplace3.png)
   4. 设置 Chart 值后，单击 *Next*，然后单击 *Install*。


3. 验证安装。

   要验证 Kasten K10 是否已正确安装，请在 “kasten-io” 命名空间中运行以下命令并查看所有 K10 pod 的状态：

   ```
   kubectl get pods --namespace kasten-io --watch
   ```

   Pod 需要几分钟后才能全部出现并显示 “Running” 状态：

   ```
   kubectl get pods --namespace kasten-io

   NAMESPACE NAME READY STATUS RESTARTS AGE
   kasten-io aggregatedapis-svc-b45d98bb5-w54pr 1/1 Running 0 1m26s
   kasten-io auth-svc-8549fc9c59-9c9fb 1/1 Running 0 1m26s
   kasten-io catalog-svc-f64666fdf-5t5tv 2/2 Running 0 1m26s
   ...
   ```



> 注意：
> 如果 pod 卡在其他状态，请参阅[支持文档](https://docs.kasten.io/latest/operating/support.html#support)以进一步进行调试。

## 5 访问 K10 仪表板
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Kasten K10 仪表板默认不对外暴露。要建立连接，请运行以下 `kubectl` 命令：

```
kubectl --namespace kasten-io port-forward service/gateway 8080:8000
```

> 注意：
> 如果你安装的 Kasten K10 的版本名称不是 k10（通过安装命令中的 `--name` 选项指定），请将上述 URL 中最后面的 `k10` 替换为你的版本名称。修改后的 URL 的格式为 `http://127.0.0.1:8080//#/`。

> 注意：
> 如果你使用 GKE 并且想要在没有本地 `kubectl` 权限的情况下访问仪表板，请参阅[直接在 Google Cloud Console 中访问 K10 仪表板](https://docs.kasten.io/latest/access/gcp_details/gcp_console_dashboard.html)。

如果要[直接访问](https://docs.kasten.io/latest/access/authentication.html#id5) K10 仪表板，你需要正确配置身份验证来保护访问。有关详细信息，请参阅 [Kubernetes 身份验证](https://kubernetes.io/docs/reference/access-authn-authz/authentication/)。

在本指南中，我们概述了配置基本身份验证和令牌身份验证的步骤，你可以选择其中一种身份验证方法。

### 5.1 基本身份验证

[基本身份验证](https://docs.kasten.io/latest/accecess/authentication.html#id6)能让你使用用户名和密码来保护对 K10 仪表板的访问。

首先，使用[在线工具](http://www.htaccesstools.com/htpasswd-generator/)或系统上的 `htpasswd` 命令（大多数系统上都支持）生成 [htpasswd](https://httpd.apache.org/docs/2.4/programs/htpasswd.html) 凭证来启用基本身份验证。生成后，你需要在 `helm install` 或 `helm upgrade` 命令中使用以下标志来提供获取的字符串：

```
--set auth.basicAuth.enabled=true \
--set auth.basicAuth.htpasswd='example:$apr1$qrAVXu.v$Q8YVc50vtiS8KPmiyrkld0'
```

或者，你可以使用由 `htpasswd` 创建的文件中包含的 secret。secret 必须位于 K10 命名空间中，密钥名为 “auth”，值为使用 `htpasswd` 生成的密码：

```
--set auth.basicAuth.enabled=true \
--set auth.basicAuth.secretName=my-basic-auth-secret
```

### 5.2 令牌身份验证

[令牌身份验证](https://docs.kasten.io/latest/access/authentication.html#id7)能让你使用任何可以被 Kubernetes 服务器验证的令牌。有关令牌身份验证的更多信息，请参阅：

- [获取令牌](https://docs.kasten.io/latest/access/authentication.html#id8)
- [身份验证策略](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#authentication-strategies)

1. 在 `helm install` 或后续的 `helm upgrade` 命令中使用以下标志，从而启用令牌身份验证：

   ```
   --set auth.tokenAuth.enabled=true
   ```


2. 然后，提供要在访问仪表板时使用的持有者令牌：

   [![rancher veeam kasten k10 token login1](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_token-login1.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_token-login1.png)

最常见的令牌类型是 ServiceAccount 持有者令牌。

1. 你可以使用 `kubectl` 从具有适当权限的 ServiceAccount 中提取此类令牌。


   1. 获取 SA secret：

      ```
      sa_secret=$(kubectl get serviceaccount my-kasten-sa -o jsonpath="{.secrets[0].name}" --namespace kasten-io)
      ```


   2. 提取令牌：

      ```
      kubectl get secret $sa_secret --namespace kasten-io -ojsonpath="{.data.token}{'\n'}" | base64 --decode
      ```




2. 你也可以创建一个新的 ServiceAccount 来提取令牌：

   ```
   kubectl create serviceaccount my-kasten-sa --namespace kasten-io
   ```

   在这种情况下，你需要为该账号创建角色绑定或集群角色绑定，从而确保该账号具有适当的 K10 权限。如需详细了解必要的 K10 权限，请参阅[授权](https://docs.kasten.io/latest/access/authorization.html#authz)。

### 5.3 Kasten K10 仪表板概览

K10 仪表板分为几个部分，如下所述。

[![rancher veeam kasten k10 dashboard welcome](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_dashboard-welcome.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_dashboard-welcome.png)

> 提示：
> 首次访问 K10 仪表板，或通过 [Settings](https://docs.kasten.io/latest/usage/overview.html#k10-settings) 页面上的仪表板选项进行访问时，你会看到 K10 仪表板的导览。

K10 仪表板的上方会显示当前映射到命名空间的应用程序、系统中可能存在的策略以及集群备份数据占用情况的概览：

[![rancher veeam kasten k10 dashboard overview top](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_dashboard_overview-top.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_dashboard_overview-top.png)

在过滤具有状态服务（定义为包含持久卷）的应用程序后，此界面将应用程序进一步分类为：

- Unmanaged：没有保护此对象的策略。
- Non-compliant：具有应用于此对象的策略，但策略相关的操作失败（原因可能是底层存储缓慢、配置问题等）或尚未调用操作。
- Compliant：这些对象具有策略并且遵守策略 SLA。

> 提示：
> 你可以通过单击 *Compliant*、*Non-Compliant* 或 *Unmanaged* 按钮来过滤视图。

K10 将命名空间等同于应用程序，因此更加易于使用并且与 Kubernetes 的最佳实践保持一致，更容易使用 RBAC，并能镜像最常见的应用程序部署模式。但是，你可以将策略定义为操作多个命名空间（后文会介绍），或者仅对单个命名空间中的应用程序的子集进行操作。

假设你已经安装了应用程序。单击仪表板上的 *Applications* 后你将看到以下视图：

[![rancher veeam kasten k10 dashboard apps1](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_dashboard-apps1.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_dashboard-apps1.png)

一个应用程序由多个 Kubernetes 资源和工作负载组成，其中包括 Deployment 和 Statefulset：

[![rancher veeam kasten k10 dashboard apps2](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_dashboard-apps2.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_dashboard-apps2.png)

K10 策略能自动化你的数据管理工作流程。策略定义了要执行的操作（例如制作快照）、操作的执行频率或计划，以及如何选择要管理的资源。

[![rancher veeam kasten k10 dashboard overview top](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_dashboard_overview-top.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_dashboard_overview-top.png)

单击主仪表板中的 *Policies* 后，你会发现安装时没有创建默认策略，但是你可以从此页面或上文提及的 *Applications* 页面中创建策略：

[![rancher veeam kasten k10 dashboard policies](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_dashboard-policies.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_dashboard-policies.png)

## 6 创建 location 配置文件
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------

K10 可以在集群内调用保护操作（例如快照）而无需额外的凭证。如果 K10 运行在主流的公有云中并且仅进行单集群操作，这可能就足够了。但是，对于大多数生产情况来说，这还不足够。在这种情况下，执行真正的备份、启用跨集群和跨云应用程序迁移以及启用灾难恢复是必要的。

要启用跨越集群生命周期的操作，K10 需要配置为能访问外部对象存储或外部 NFS 文件存储，而这是通过 location 配置文件实现的：

[![rancher veeam kasten k10 location profiles1](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_location-profiles1.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_location-profiles1.png)

你可以单击仪表板右上角的 *Settings* 图标，或通过 [CRD-based Profiles API](https://docs.kasten.io/latest/api/profiles.html#api-profile) 来创建配置文件。Location 配置文件用于使用快照创建备份、跨集群或跨云平台移动应用程序及其数据，并将备份导出/导入集群。

要创建 location 配置文件，单击配置文件页面上的 *New Profile*：

[![rancher veeam kasten k10 location profiles2](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_location-profiles2.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_location-profiles2.png)

在对象存储位置进行导出或导入时，你需要选择对象存储提供程序、存储桶的区域（如果在公共云中使用）以及存储桶名称。如果该名称的存储桶不存在，则会创建该存储桶。

如果使用了不受支持的云厂商的 S3 兼容对象存储系统，则需要指定 S3 端点 URL，并且可能需要禁用 SSL 验证。仅建议在测试场景下禁用 SSL 验证。

> 注意：
> 选择云厂商（例如 AWS 或 Microsoft Azure）后，云厂商对应的选项（例如 IAM Roles）则会显示。

单击 *Validate and Save* 后将创建配置文件，然后会出现类似以下的配置文件：

[![rancher veeam kasten k10 location profiles3](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_location-profiles3.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_location-profiles3.png)

## 7 创建策略
-----------------------------------------------------------------------------------------------------------------------------------------------------

要使用 K10 来保护应用程序，你通常需要创建策略。你需要了解以下的三个概念：

- **快照和备份**：你可能需要使用这两种数据捕获方式中的一种或两种，具体取决于你的环境和要求。
- **计划**：你可以指定应用程序捕获频率和快照/备份保留时间。
- **选择**：确定受策略保护的应用程序。你还可以通过过滤资源来限制每个应用程序捕获的内容。

本节演示如何在 K10 策略中使用这些概念来保护应用程序。

K10 将应用程序定义为命名空间中的 Kubernetes 资源的集合，这些资源与以下内容关联：

- 工作负载（例如 ConfigMap 和 Secret）
- 应用程序使用的非命名空间资源（例如 StorageClass）
- Kubernetes 工作负载（包括 Deployment、StatefulSet、独立的 pod 等）
- Helm v3 提供的 Deployment 和版本信息
- 所有持久存储资源（例如 PersistentVolumeClaim 和 PersistentVolume）

你可以在策略页面从零开始创建策略，但是，为应用程序定义策略最简单方法是单击主仪表板中的 *Applications*。这样，你能查看 Kubernetes 集群中的所有应用程序：

[![rancher veeam kasten k10 dashboard apps1](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_dashboard-apps1.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_dashboard-apps1.png)

要保护 Unmanaged 的应用程序，只需单击 *Create a Policy* 以打开 *New Policy* 对话框即可：

[![rancher veeam kasten k10 policies create1](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_policies_create1.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_policies_create1.png)

### 7.1 快照和备份

操作执行是所有策略的中心。你可以先选择具有可选备份（称为 **export**）的快照操作。

有关详细信息，请参阅 Kasten 文档中的[快照和备份](https://docs.kasten.io/latest/usage/protect.html#snapshots-and-backups)。

#### 7.1.1 快照

快照是 K10 中持久数据捕获的基础。它们通常用于应用程序使用的磁盘卷 (PVC/PV) 的上下文中，但也可以应用于应用程序级别的数据捕获（例如使用 [Kanister](https://docs.kasten.io/latest/kanister/kanister.html#kanister)）。

[![rancher veeam kasten k10 policies create2](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_policies_create2.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_policies_create2.png)

> 注意：
> 一些公有云厂商（包括 AWS、Azure 和 Google Cloud）实际上将快照存储在对象存储中，而且它们的保留与主卷的生命周期是分开的。但是，并非所有公有云都是这样的，因此独立备份更加安全。请查阅你的云厂商的文档以获取更多信息。

在大多数存储系统中，快照是非常高效的，而且对主要工作负载的性能影响非常低，不需要停机，支持快速恢复，并支持增量数据捕获。

快照的存储通常会受到限制，例如每个卷/存储阵列的最大快照数量相对较低。最重要的是，快照并不都是持久的。如果发生灾难性的存储系统故障，你的快照和主要数据都会被破坏。此外，在一些存储系统中，快照的生命周期与源卷是相关联的。因此，如果卷被删除，那么所有关联的快照都可能被自动回收。

> 提示：
> 强烈建议你备份应用程序的快照以确保持久性。

#### 7.1.2 备份

备份能克服应用程序和卷快照的限制，其原理是通过将快照转换为独立于基础设施的格式，在存储到外部对象存储或 NFS 卷之前对其进行重复数据删除、压缩和加密。

要将快照转换为备份，请在策略创建期间启用 *Enable Backups via Snapshot Exports*：

[![rancher veeam kasten k10 policies create3](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_policies_create3.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_policies_create3.png)

### 7.2 调度

K10 调度有四个组成部分：

- 操作频率：执行主快照操作的频率
- 导出频率：将快照导出到备份的频率
- 保留计划：如何轮换和保留快照和备份
- 时间：执行主要快照操作的时间

#### 7.2.1 操作频率

快照的频率可以设置为 Hourly、Daily、Weekly、Monthly、Yearly 或 On Demand。默认情况下，Hourly 快照会在整点执行，而 Weekly、Monthly 和 Yearly 快照会在 UTC 零点执行。

[![rancher veeam kasten k10 backup frequency](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_backup-frequency.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_backup-frequency.png)

你还可以指定执行计划操作的时间以及单个频率内执行多个操作的子频率。在保护 Kubernetes 对象或小型数据集时，按小时执行操作会非常有用。有关详细信息，请参阅 Kasten 文档中的[高级调度选项](https://docs.kasten.io/latest/usage/protect.html#advanced-schedule-options)。

> 警告：
> 请不要加紧底层存储基础设施或存储 API 速率的限制。此外，子频率与保留时间（如下所述）也是相互作用的。例如，如果你以 15 分钟的间隔保留 24 小时的快照，那么实际上只会保留 6 小时的快照。

#### 7.2.2 导出频率

启用 *Enable Backups via Snapshot Exports* 后，快照将作为备份导出。默认情况下会导出每个快照，但你可以通过选择 Daily、Weekly、Monthly 或 Yearly 的导出频率将其限制为一个子集：

[![rancher veeam kasten k10 export frequency](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_export-frequency.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_export-frequency.png)

#### 7.2.3 保留计划

K10 能够使用 [GFS 保留方案](https://en.wikipedia.org/wiki/Backup_rotation_scheme#Grandfather-father-son)来节省成本和确保合规性。如果你使用这个备份轮换方案，Hourly 快照和备份会每小时轮换一次，然后其中一个会逐渐轮换成 Daily 频率；同理，Daily 快照和备份会每天轮换一次，然后其中一个会逐渐轮换成 Weekly 频率，以此类推。你可以设置需要保留的 Hourly、Daily、Weekly、Monthly 和 Yearly 副本数，K10 将按照设置进行清理。

[![rancher veeam kasten k10 snapshot retention schedule](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_snapshot-retention-schedule.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_snapshot-retention-schedule.png)

> 注意：
> 不支持为 On Demand 策略设置保留计划。

默认情况下，备份保留计划与快照保留计划是相同的。你也可以设置不同的计划。你可以创建保留有限快照数量的策略，从而在意外中断时进行快速恢复，同时存储大量备份。如果卷仅支持有限数量的快照，但需要大量备份保留数来实现合规性，这种单独的保留计划就非常有用。

[![rancher veeam kasten k10 backup retention schedule](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_backup-retention-schedule.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_backup-retention-schedule.png)

通过将 `k10.kasten.io/doNotRetire: "true"` 标签添加到为策略运行而创建的 [RunAction](https://docs.kasten.io/latest/api/actions.html#api-run-action)，你可以从保留计数中保留和省略策略创建的快照和备份。

> 注意：
> 策略的保留计划不适用于[手动运行策略](https://docs.kasten.io/latest/usage/protect.html#manual-policy-runs)生成的快照和备份。你需要清理手动运行策略期间创建的任何工件。

#### 7.2.4 定时

默认情况下，设置为 Hourly 执行的操作会在整点执行，而其他频率的操作会在 UTC 午夜执行。

你可以取消隐藏 *Advanced Options*，从而选择在频率间隔内执行操作的次数。例如，如果操作频率是 Daily，你可以指定开始操作的具体小时和分钟：

[![rancher veeam kasten k10 timing1](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_timing1.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_timing1.png)

你还可以设置哪些快照和备份需要保留更长时间：

[![rancher veeam kasten k10 timing2 snapshot retention](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_timing2-snapshot-retention.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_timing2-snapshot-retention.png)

> 提示：
> 你可以选择以本地时间或 UTC 时间来显示和输入时间，但所有时间都将转换为 UTC，并且不会更改为夏令时。

### 7.3 选择

在 K10 中，你可以通过名称或标签来指定要绑定到策略的应用程序。

#### 7.3.1 应用程序名称

在 K10 中，将策略应用于应用程序的最直接的方法是使用名称（源自命名空间名称）。你甚至可以为同一策略选择多个应用程序名称。

如果你需要跨相似应用程序的策略，请使用星号 (`*`) 通配符。例如，如果你指定了 `mysql-*`，K10 将匹配所有名称以 `mysql-` 开头的应用程序。

[![rancher veeam kasten k10 selection by name wildcard](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_selection-by-name-wildcard.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_selection-by-name-wildcard.png)

> 注意：
> 对于需要跨所有应用程序的策略，请单独使用星号通配符。

#### 7.3.2 应用程序标签

你还可以使用标签将策略绑定到多个应用程序。例如，你可以保护所有使用 MongoDB 或使用 “gold” 标签进行注释的应用程序。命名空间、deployment 和 statefulset 的标签会被匹配。如果选择了多个标签，将执行并集（逻辑 OR），也就是让所有应用程序至少匹配一个标签。

标签可用于创建前瞻性策略，因为此类策略将自动应用于后续带有匹配标签的任何应用程序。例如，如果你使用了 “heritage: Tiller”（Helm v2）或 “heritage: Helm”（Helm v3）标签，由于标签会应用于 Helm 包管理器创建的任何 Kubernetes 工作负载，因此选择器会将策略应用于任何 Helm 新部署的应用程序。

[![rancher veeam kasten k10 selection by label](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_selection-by-label.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_selection-by-label.png)

#### 7.3.3 其他资源

K10 还可以保护集群级别的资源，而不针对具体应用程序。要使用此选项，请选择 *None*：

[![rancher veeam kasten k10 selection cluster scoped](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_selection-cluster-scoped.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_selection-cluster-scoped.png)

有关保护集群级别资源的更多信息，请参阅[集群级别资源](https://docs.kasten.io/latest/usage/clusterscoped.html#clusterscoped)。

#### 7.3.4 自定义

你可以通过以下方式进一步自定义 K10 应用程序保护策略：

- [命名空间排除](https://docs.kasten.io/latest/usage/protect.html#namespace-exclusion)
- [例外](https://docs.kasten.io/latest/usage/protect.html#exceptions)
- [资源过滤](https://docs.kasten.io/latest/usage/protect.html#resource-filtering)

## 8 使用策略
-------------------------------------------------------------------------------------------------------------------------------------------------------------

创建策略并导航回主仪表板后，你将看到选定的应用程序从 Unmanaged 切换到 Non-Compliant。也就是说，策略涵盖了对象，但尚未执行任何操作。当快照和备份已运行且应用程序进入受保护状态时，应用程序将切换到 Compliant 状态。你还可以向下滚动页面，从而查看活动、每个快照所用的时间以及生成的工件。

[![rancher veeam kasten k10 policy activity](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_policy-activity.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_policy-activity.png)

> 注意：
> 你可以单击进行中或已完成的 Job 来获取更详细的信息。

### 8.1 手动运行策略

你可以通过单击策略上的 *run once* 按钮来手动运行策略。此操作创建的工件都不符合自动停用的条件，因此需要手动清理。

[![rancher veeam kasten k10 policy detail manual](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_policy-detail-manual.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_policy-detail-manual.png)

### 8.2 恢复现有应用程序

你可以通过 *Applications* 页面来恢复应用程序。要恢复应用程序，单击 *Applications* 卡片中的 *restore* 图标：

[![rancher veeam kasten k10 restore app1](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_restore-app1.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_restore-app1.png)

> 注意：
> 虽然 UI 中使用了 “export” 术语，但是恢复备份不需要使用导入策略。只要在将应用程序恢复到其他集群时才需要导入策略。

然后，可以选择一个还原点。UI 中将它们区分为自动生成（使用备份策略）和手动生成。

[![rancher veeam kasten k10 restore app2](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_restore-app2.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_restore-app2.png)
还原点可能包括具有相同数据的快照（集群本地）和备份（已导出到集群外）。当快照和备份都存在时，UI 能让你选择要用于恢复应用程序的实例：

[![rancher veeam kasten k10 restore app3](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_restore-app3.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_restore-app3.png)
选择还原点后，一个侧面板将会打开，其中包含还原点的详细信息，你可以在开始恢复应用程序之前进行预览：

[![rancher veeam kasten k10 restore app4](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_restore-app4.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_restore-app4.png)

单击 *Restore* 时，系统会自动将整个应用程序堆栈重新创建到选定的命名空间中，其中不仅包括与原始应用程序相关的数据，还包括版本化的容器镜像。

> 注意：
> 恢复的 PersistentVolume 可能没有原始 PersistentVolume 中的注释。

恢复完成后，你可以返回应用程序并验证它是否已恢复到获取还原点时的状态。

### 8.3 恢复已删除的应用程序

恢复已删除的应用程序的流程也差不多，但默认情况下，已删除的应用程序不会显示在 **Applications** 页面上。因此，你需要过滤并选择 *Removed*：

[![rancher veeam kasten k10 restore deleted app](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_restore-deleted-app.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_restore-deleted-app.png)

过滤器生效后，你会看到 K10 保护过但不再存在的应用程序。现在，你可以按照正常的恢复流程来恢复它们。

## 9 总结
---------------------------------------------------------------------------------------------------------------------------------

SUSE Rancher 让企业能通过统一的安全、策略和用户管理方法来简化多集群 Kubernetes 操作。而 Kasten K10 by Veeam 能使 Kubernetes 原生应用程序的备份和恢复、灾难恢复和移动变得简单。SUSE 和 Veeam 共同为企业提供降低风险和加快云原生实现所需的工具。

在本指南中，你了解了如何将 Kasten K10 无缝部署到 Rancher Kubernetes 环境中、如何创建策略驱动的备份，以及如何恢复应用程序。

如需了解更多信息，请参阅：

- [使用 Kasten K10 by Veeam 和 SUSE Rancher 来保护云原生工作负载 - 演示视频](https://youtu.be/c_mSNy6Q9RU)
- [Kasten K10 by Veeam 和 SUSE Rancher：企业 K8s 数据保护 - 博客文章](https://www.suse.com/c/kasten-k10-by-veeam-and-suse-rancher-enterprise-k8s-data-protection/)
- [使用 SUSE Rancher、Fleet 和 Kasten K10 部署多集群 Day 2 操作 - 博客文章](https://www.suse.com/c/deploying-multicluster-day-2-operations-with-suse-rancher-fleet-and-kasten-k10/)
- [通过 2 个简单步骤开始使用 SUSE Rancher](https://www.suse.com/products/suse-rancher/get-started/)
- [免费下载 Kasten K10 并在最多 5 个节点上使用它](https://www.kasten.io/free-kubernetes)
- [Kasten K10 文档](https://docs.kasten.io/latest/index.html)
- [Rancher 文档](https://rancher.com/docs/rancher/v2.x/en/)

