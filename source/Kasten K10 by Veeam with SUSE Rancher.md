Kasten K10 by Veeam with SUSE Rancher
=============================================================

### *Getting Started*

###### Abstract

This document provides a brief introduction and demonstration of Kasten K10 by Veeam with SUSE Rancher for enterprise cloud native backup/restore, disaster recovery, and app mobility.

 **SUSE One Partner Solution Stack**

SUSE One Partner Solution Stacks are featured, SUSE-confirmed solutions, collaboratively developed with SUSE and partner components and designed to empower users to address their needs.

 **Disclaimer:** Documents published as part of the series SUSE Technical Reference Documentation have been contributed voluntarily by SUSE employees and third parties. They are meant to serve as examples of how particular actions can be performed. They have been compiled with utmost attention to detail. However, this does not guarantee complete accuracy. SUSE cannot verify that actions described in these documents do what is claimed or whether actions described have unintended consequences. SUSE LLC, its affiliates, the authors, and the translators may not be held liable for possible errors or the consequences thereof.

**Author**: Adam Bergh, Solutions Architect, Cloud Native Technical Partnerships, Kasten by VeeamAuthor: Terry Smith, Global Partner Solutions Director, SUSE
**Author**: Gerson Guevara, IHV Solutions Architect, SUSE
**Publication Date**: 2022-07-27

[1 Introduction](#id-introduction)
[2 Technical overview](#id-technical-overview)
[3 Prerequisites](#id-prerequisites)
[4 Installing Kasten K10](#id-installing-kasten-k10)
[5 Accessing the K10 dashboard](#id-accessing-the-k10-dashboard)
[6 Creating a location profile](#id-creating-a-location-profile)
[7 Creating a policy](#id-creating-a-policy)
[8 Working with policies](#id-working-with-policies)
[9 Summary](#id-summary)

## 1 Introduction
-------------------------------------------------------------------------------------------------------------------------------------------

### 1.1 Motivation

Organizations are shifting to cloud native, leveraging containerized workloads and Kubernetes management platforms like SUSE Rancher. The goal is to gain greater flexibility, scale, and resilience to accelerate innovation and quickly adjust to dynamic conditions. In this always-on IT environment, application mobility and data protection are critical considerations.

[![logo lockup suse rancher veeam kasten hor dark new](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/logo-lockup_suse-rancher_veeam-kasten_hor_dark_new.svg)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/logo-lockup_suse-rancher_veeam-kasten_hor_dark_new.svg)

The Kasten K10 by Veeam® data management platform provides enterprise operations teams with an easy-to-use, scalable, and secure system for backup and restore, disaster recovery, and mobility of cloud-native applications.

### 1.2 Scope

This guide provides an overview of the steps to install and set up Kasten K10 by Veeam in your SUSE Rancher Kubernetes environment and to perform a simple backup and restore of an application.

### 1.3 Audience

This document is intended for IT operations teams, backup administrators, DevOps and DevSecOps teams, and others who are responsible for ensuring business continuity, disaster recovery, ransomware and threat reduction, and application migration for cloud native landscapes.

## 2 Technical overview
-------------------------------------------------------------------------------------------------------------------------------------------------------

The Kasten K10 by Veeam® data management platform has deep integrations with SUSE Rancher and comes with an extensive ecosystem of support across Kubernetes distributions and cloud platforms. This provides enterprise operations teams the flexibility to choose the deployment environments that best meet their needs - on-premises, public cloud, and hybrid. K10 is policy-driven and extensible. It delivers enterprise features such as full-spectrum consistency, database integration, automatic application discovery, multi-cloud mobility, and a powerful web-based user interface.

[![rancher veeam kasten architecture 1](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_architecture-1.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_architecture-1.png)

## 3 Prerequisites
---------------------------------------------------------------------------------------------------------------------------------------------

For this guide, you will need the following:

- SUSE Rancher
    
    In this guide, we use Rancher 2.6.
    
    See [SUSE Rancher installation guide](https://rancher.com/docs/rancher/v2.6/en/installation/requirements/) for more information.
- Kubernetes cluster managed by SUSE Rancher Any CNCF-certified Kubernetes cluster can be used.
    
    See [SUSE Rancher Support Matrix](https://www.suse.com/suse-rancher/support-matrix/all-supported-versions/rancher-v2-6-3/)
- Storage for backup target
    
    An external backup storage target, such as an NFS file server or cloud object store. This document will use an external, S3-compatible object storage bucket.
- User application to demonstrate backup and restore capability
    
    For example, WordPress can be easily installed by [Helm chart](https://bitnami.com/stack/wordpress/helm).

K10 can be installed in a variety of different environments. To ensure a smooth installation experience, you can use the primer tool to perform several pre-flight checks to make sure that the prerequisites are met. This tool runs in a pod in the cluster and does the following:

- Validates that the Kubernetes settings meet the K10 requirements.
- Catalogs the available StorageClasses.
- If a CSI provisioner exists, it will also perform a basic validation of the cluster’s CSI capabilities and any relevant objects that may be required.
    
    See [CSI pre-flight checks](https://docs.kasten.io/latest/install/storage.html#csi-preflight) in the documentation for more details.

Run the following command to deploy the primer tool:

```
curl https://docs.kasten.io/tools/k10_primer.sh | bash
```

> Note
> This will create and clean up a ServiceAccount and ClusterRoleBinding to perform sanity checks on your Kubernetes cluster.

## 4 Installing Kasten K10
-------------------------------------------------------------------------------------------------------------------------------------------------------------

Kasten K10 can be easily deployed from the SUSE Rancher Apps &amp; Marketplace.

1. Create a new [namespace](https://rancher.com/docs/rancher/v2.6/en/project-admin/namespaces/) for the Kasten K10 application.
    
    
    1. In the SUSE Rancher user interface (UI), navigate to *Clusters* → *Project/Namespaces*.
        
        [![rancher veeam kasten step1 1](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_step1-1.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_step1-1.png)
    2. Create a "kasten-io" namespace for Kasten K10.
        
        [![rancher veeam kasten k10 create namespace](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_create-namespace.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_create-namespace.png)
    
    
2. Install Kasten K10.
    
    
    1. Navigate to *Apps &amp; Marketplace* &gt; *Charts* within the SUSE Rancher UI and search for “Kasten.”
        
        [![rancher veeam kasten k10 marketplace1](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_marketplace1.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_marketplace1.png)
    2. Select the K10 chart and click *Install*.
        
        [![rancher veeam kasten k10 marketplace2](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_marketplace2.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_marketplace2.png)
    3. Select the namespace "kasten-io” from the Namespace drop-down box. Optionally select *Customize Helm options before install* to customize the deployment. See the [Complete list of Helm options](https://docs.kasten.io/latest/install/advanced.html#complete-list-of-k10-helm-options) for detailed descriptions.
        
        [![rancher veeam kasten k10 marketplace3](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_marketplace3.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_marketplace3.png)
    4. After setting your chart values, click *Next*, then click *Install*.
    
    
3. Validate the installation.
    
    To validate that Kasten K10 has been properly installed, run the following command in the "kasten-io" namespace and watch for the status of all K10 pods:
    
    ```
    kubectl get pods --namespace kasten-io --watch
    ```
    
    It may take a couple of minutes for all pods to come up and display "Running" status.
    
    ```
    kubectl get pods --namespace kasten-io
    
    NAMESPACE NAME READY STATUS RESTARTS AGE
    kasten-io aggregatedapis-svc-b45d98bb5-w54pr 1/1 Running 0 1m26s
    kasten-io auth-svc-8549fc9c59-9c9fb 1/1 Running 0 1m26s
    kasten-io catalog-svc-f64666fdf-5t5tv 2/2 Running 0 1m26s
    ...
    ```
    
    

> Note
> In the unlikely scenario that pods are stuck in any other state, see the [support documentation](https://docs.kasten.io/latest/operating/support.html#support) to debug further.

## 5 Accessing the K10 dashboard
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------

The Kasten K10 dashboard is not exposed externally by default. To establish a connection, use the following `kubectl` command:

```
kubectl --namespace kasten-io port-forward service/gateway 8080:8000
```

> Note
> If you installed Kasten K10 with a different release name than k10 (specified via the `--name` option in the install command), replace the last occurrence of "k10" in the above URL with the specified release name. The revised URL would look like `http://127.0.0.1:8080//#/`.

> Note
> If you are running on GKE and want to access the dashboard without local `kubectl` access, see [K10 Dashboard Directly From the Google Cloud Console](https://docs.kasten.io/latest/access/gcp_details/gcp_console_dashboard.html).

[Direct access](https://docs.kasten.io/latest/access/authentication.html#id5) to the K10 dashboard requires a properly configured authentication method to secure access. For more information, see [Kubernetes authentication](https://kubernetes.io/docs/reference/access-authn-authz/authentication/).

For this guide, we provide an overview of the steps to configure basic authentication or token authentication. Choose one of these authentication methods to proceed.

### 5.1 Basic authentication

[Basic authentication](https://docs.kasten.io/latest/accecess/authentication.html#id6) allows you to protect access to the K10 dashboard with a user name and password.

Enable basic authentication by first generating [htpasswd](https://httpd.apache.org/docs/2.4/programs/htpasswd.html) credentials using either an [online tool](http://www.htaccesstools.com/htpasswd-generator/) or via the `htpasswd` command found on most systems. When generated, you need to supply the resulting string with the `helm install` or `helm upgrade` command using the following flags:

```
--set auth.basicAuth.enabled=true \
--set auth.basicAuth.htpasswd='example:$apr1$qrAVXu.v$Q8YVc50vtiS8KPmiyrkld0'
```

Alternatively, you can use an existing secret contained in a file created with `htpasswd`. The secret must be in the K10 namespace with the key named "auth" and the value as the password generated using `htpasswd`.

```
--set auth.basicAuth.enabled=true \
--set auth.basicAuth.secretName=my-basic-auth-secret
```

### 5.2 Token authentication

[Token authentication](https://docs.kasten.io/latest/access/authentication.html#id7) allows the use of any token that can be verified by the Kubernetes server. For more information about token authentication, see:

- [Obtaining Tokens](https://docs.kasten.io/latest/access/authentication.html#id8)
- [Authentication Strategies](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#authentication-strategies)

1. Enable token authentication by using the following flag as part of the initial `helm install` or subsequent `helm upgrade` command.
    
    ```
    --set auth.tokenAuth.enabled=true
    ```
    
    
2. Next, provide a bearer token that will be used when accessing the dashboard.
    
    [![rancher veeam kasten k10 token login1](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_token-login1.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_token-login1.png)

The most common token type that you can use is a service account bearer token.

1. You can use `kubectl` to extract such a token from a service account that you know has the proper permissions.
    
    
    1. Get the SA secret
        
        ```
        sa_secret=$(kubectl get serviceaccount my-kasten-sa -o jsonpath="{.secrets[0].name}" --namespace kasten-io)
        ```
        
        
    2. Extract the token
        
        ```
        kubectl get secret $sa_secret --namespace kasten-io -ojsonpath="{.data.token}{'\n'}" | base64 --decode
        ```
        
        
    
    
2. Alternatively, you can create a new service account from which to extract the token.
    
    ```
    kubectl create serviceaccount my-kasten-sa --namespace kasten-io
    ```
    
    In this case, you need to create a role binding or cluster role binding for the account to ensure that it has the appropriate permissions for K10. To learn more about the necessary K10 permissions, see [Authorization](https://docs.kasten.io/latest/access/authorization.html#authz).

### 5.3 Kasten K10 dashboard overview

The K10 dashboard is divided into several different sections, described below.

[![rancher veeam kasten k10 dashboard welcome](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_dashboard-welcome.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_dashboard-welcome.png)

> Tip
> A guided tour of the K10 dashboard is available when the K10 dashboard is accessed for the first time or via the dashboard option on the [Settings](https://docs.kasten.io/latest/usage/overview.html#k10-settings) page.

The top of the K10 dashboard displays a list of applications currently mapped to namespaces, any policies that might exist in the system, and a summary of the cluster’s backup data footprint.

[![rancher veeam kasten k10 dashboard overview top](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_dashboard_overview-top.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_dashboard_overview-top.png)After filtering for applications that have stateful services (defined as containing a persistent volume), this screen further categorizes applications as:

- Unmanaged: There are no protection policies that cover this object.
- Non-compliant: A policy applies to this object, but the actions associated with the policy are failing (because of underlying storage slowness, configuration problems, etc.) or the actions have not been invoked yet.
- Compliant: These objects have policies and the policy SLAs are being respected.

> Tip
> You can filter the view by clicking the *Compliant*, *Non-Compliant*, or *Unmanaged* buttons.

The K10 platform equates namespaces to applications for ease of use and consistency with Kubernetes best practices, use of role-based authentication controls (RBAC), and to mirror the most common application deployment patterns. However, as shown later, policies can be defined to operate on more than one namespace or only operate on a subset of an application residing in a single namespace.

Assuming you have already installed applications, clicking the *Applications* card on the dashboard will take you to the following view.

[![rancher veeam kasten k10 dashboard apps1](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_dashboard-apps1.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_dashboard-apps1.png)

An application is made up of multiple Kubernetes resources and workloads, including deployments and stateful sets.

[![rancher veeam kasten k10 dashboard apps2](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_dashboard-apps2.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_dashboard-apps2.png)

K10 policies are used to automate your data management workflows. Policies combine actions you want to take (such as making a snapshot), frequency or schedule for how often you want to perform the action, and selection criteria for the resources you want to manage.

[![rancher veeam kasten k10 dashboard overview top](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_dashboard_overview-top.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_dashboard_overview-top.png)

When you click the *Policies* card in the main dashboard, you notice that no default policies are created at install time, but a policy can be either created from this page or from the application page shown earlier.

[![rancher veeam kasten k10 dashboard policies](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_dashboard-policies.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_dashboard-policies.png)

## 6 Creating a location profile
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------

K10 can invoke protection operations, such as snapshots, within a cluster without requiring additional credentials, and this may be sufficient if K10 is running in the major public clouds and actions are limited to a single cluster. It is not sufficient for most production situations, where performing real backups, enabling cross-cluster and cross-cloud application migration, and enabling disaster recovery are essential.

To enable these actions that span the lifetime of any one cluster, K10 needs to be configured with access to external object storage or external NFS file storage. This is accomplished with location profiles.

[![rancher veeam kasten k10 location profiles1](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_location-profiles1.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_location-profiles1.png)

Profile creation can be accessed from the *Settings* icon in the top-right corner of the dashboard or via the [CRD-based Profiles API](https://docs.kasten.io/latest/api/profiles.html#api-profile). Location profiles are used to create backups from snapshots, move applications and their data across clusters and potentially across different clouds, and to subsequently import these backups or exports into another cluster.

To create a location profile, click *New Profile* on the profiles page.

[![rancher veeam kasten k10 location profiles2](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_location-profiles2.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_location-profiles2.png)

When exporting to or importing from an object storage location, you are required to pick an object storage provider, a region for the bucket if being used in a public cloud, and the bucket name. If a bucket with the given name does not exist, it will be created.

If an S3-compatible object storage system is used that is not hosted by one of the supported cloud providers, an S3 endpoint URL will need to be specified and optionally, SSL verification might need to be disabled. Disabling SSL verification is only recommended for test setups.

> Note
> When certain cloud providers (like AWS or Microsoft Azure) are selected, provider-specific options (such as IAM Roles) will appear for configuration.

When you click *Validate and Save*, the configuration profile will be created and a profile similar to the following will appear:

[![rancher veeam kasten k10 location profiles3](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_location-profiles3.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_location-profiles3.png)

## 7 Creating a policy
-----------------------------------------------------------------------------------------------------------------------------------------------------

Protecting an application with K10, is usually accomplished by creating a policy. Understanding these three concepts is essential:

- **Snapshots and Backups**: You may require just one or both of these data capture mechanisms, depending on your environment and requirements.
- **Scheduling**: You specify the application capture frequency and snapshot/backup retention to meet your objectives.
- **Selection**: You identify the applications protected by a policy. You can also filter resources to restrict what is captured on a per-application basis.

This section demonstrates how to use these concepts in the context of a K10 policy to protect applications.

K10 defines an application as a collection of namespaced Kubernetes resources associated with

- a workload (such as ConfigMaps and Secrets),
- relevant non-namespaced resources used by the application (such as StorageClasses),
- Kubernetes workloads (including Deployments, StatefulSets, standalone pods, etc.),
- deployment and release information available from Helm v3,
- and all persistent storage resources (such as PersistentVolumeClaims and PersistentVolumes).

While you can always create a policy from scratch through the policies page, the easiest way to define policies for unprotected applications is to click the *Applications* card in the main dashboard. This will allow you to see all applications in your Kubernetes cluster.

[![rancher veeam kasten k10 dashboard apps1](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_dashboard-apps1.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_dashboard-apps1.png)

To protect any unmanaged application, simply click *Create a Policy* to open the *New Policy* dialog.

[![rancher veeam kasten k10 policies create1](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_policies_create1.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_policies_create1.png)

### 7.1 Snapshots and backups

All policies center around the execution of actions. You start by selecting the snapshot action with an optional backup (called an **export**).

See [Snapshots and Backups](https://docs.kasten.io/latest/usage/protect.html#snapshots-and-backups) in the Kasten documentation for more details.

#### 7.1.1 Snapshots

Snapshots form the basis of persistent data capture in K10. They are typically used in the context of disk volumes (PVC/PVs) used by the application but can also apply to application-level data capture (such as with [Kanister](https://docs.kasten.io/latest/kanister/kanister.html#kanister)).

[![rancher veeam kasten k10 policies create2](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_policies_create2.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_policies_create2.png)

> Note
> Several public cloud providers (including AWS, Azure, and Google Cloud) actually store snapshots in object storage, and they are retained independent of the lifecycle of the primary volume. However, this is not true of all public clouds, and an independent backup would provide essential safety. Check with your cloud provider’s documentation for more information.

Snapshots, in most storage systems, are very efficient and have a very low performance impact on the primary workload, require no downtime, support fast restore times, and enable incremental data capture.

Storage snapshots usually suffer from constraints, such as having relatively low limits on the maximum number of snapshots per volume or per storage array. Most importantly, snapshots are not always durable. A catastrophic storage system failure will destroy your snapshots along with your primary data. Further, in several storage systems, a snapshot’s lifecycle is tied to the source volume. So, if the volume is deleted, all related snapshots might automatically be garbage collected at the same time.

> Tip
> It is highly recommended that you create backups of your application snapshots to ensure durability.

#### 7.1.2 Backups

Backups overcome the limitations of application and volume snapshots by converting them to an infrastructure-independent format, deduplicating, compressing, and encrypting them before they are stored in an external object store or NFS volume.

To convert your snapshots into backups, activate *Enable Backups via Snapshot Exports* during policy creation.

[![rancher veeam kasten k10 policies create3](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_policies_create3.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_policies_create3.png)

### 7.2 Scheduling

There are four components to K10 scheduling:

- Action frequency: how frequently the primary snapshot action should be performed
- Export frequency: how often snapshots should be exported to backups
- Retention schedule: how snapshots and backups are rotated and retained
- Timing: when the primary snapshot action should be performed

#### 7.2.1 Action frequency

Snapshots can be set to execute at an hourly, daily, weekly, monthly, or yearly frequency, or on demand. By default, hourly snapshots execute at the top of the hour, while weekly, monthly, and yearly snapshots execute at midnight UTC.

[![rancher veeam kasten k10 backup frequency](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_backup-frequency.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_backup-frequency.png)

You can also specify the time at which scheduled actions execute and sub-frequencies that execute multiple actions per frequency. Sub-hourly actions can be useful when you are protecting mostly Kubernetes objects or small data sets. See [Advanced Schedule Options](https://docs.kasten.io/latest/usage/protect.html#advanced-schedule-options) in the Kasten documentation for more information.

> Warning
> Care should be taken not to stress the underlying storage infrastructure or running into storage API rate limits. Further, sub-frequencies do also interact with retention (described below). For example, retaining 24 hourly snapshots at 15-minute intervals would only retain 6 hours of snapshots.

#### 7.2.2 Export frequency

When *Enable Backups via Snapshot Exports* is enabled, snapshots are exported as backups. By default, every snapshot is exported, but you can limit this to a subset by selecting a daily, weekly, monthly, or yearly export frequency.

[![rancher veeam kasten k10 export frequency](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_export-frequency.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_export-frequency.png)

#### 7.2.3 Retention schedule

A powerful scheduling feature in K10 is the ability to use a [GFS retention scheme](https://en.wikipedia.org/wiki/Backup_rotation_scheme#Grandfather-father-son) for cost savings and compliance. With this backup rotation scheme, hourly snapshots and backups are rotated each hour with one graduating to daily every day, daily snapshots and backups are rotated each day with one graduating to weekly, and so on. It is possible to set the number of hourly, daily, weekly, monthly, and yearly copies that need to be retained, and K10 will take care of cleanup.

[![rancher veeam kasten k10 snapshot retention schedule](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_snapshot-retention-schedule.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_snapshot-retention-schedule.png)

> Note
> It is not possible to set a retention schedule for on demand policies.

By default, the backup retention schedule is the same as the snapshot retention schedule. You can make these independent schedules, if needed. This allows you to create policies where a limited number of snapshots are retained for fast recovery from accidental outages while a larger number of backups are stored for long-term recovery. This separate retention schedule is also valuable when a limited number of snapshots are supported on the volume but a larger backup retention count is needed for compliance.

[![rancher veeam kasten k10 backup retention schedule](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_backup-retention-schedule.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_backup-retention-schedule.png)

Snapshots and backups created by scheduled runs of a policy can be retained and omitted from the retention counts by adding a `k10.kasten.io/doNotRetire: "true"` label to the [RunAction](https://docs.kasten.io/latest/api/actions.html#api-run-action) created for the policy run.

> Note
> The retention schedule for a policy does not apply to snapshots and backups produced by [manual policy runs](https://docs.kasten.io/latest/usage/protect.html#manual-policy-runs). And you will need to clean up any artifacts created by manual policy runs.

#### 7.2.4 Timing

By default, actions set to hourly execute at the top of the hour and other action frequencies execute at midnight UTC.

Unhide *Advanced Options* to select how many times actions are executed within the frequency interval. For example, if the action frequency is daily, you can specify the hour of the day and the minutes after the hour when the action is to start.

[![rancher veeam kasten k10 timing1](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_timing1.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_timing1.png)

You can also customize the retention schedule by selecting which snapshots and backups will graduate and be retained for longer periods.

[![rancher veeam kasten k10 timing2 snapshot retention](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_timing2-snapshot-retention.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_timing2-snapshot-retention.png)

> Tip
> You can toggle whether to display and enter times in local time or UTC, but all times are converted to UTC and do not change for daylight savings time.

### 7.3 Selection

In K10, you can specify which applications are bound to a policy by name or label.

#### 7.3.1 Application name

The most straightforward way to apply a policy to an application in K10 is to use its name (derived from the name of the namespace). You can even select multiple application names for the same policy.

If you need a policy to span similar applications, use the asterisk ('\*') wild card. For example, if you specify 'mysql-\_', K10 will match all applications whose names start with 'mysql-'.

[![rancher veeam kasten k10 selection by name wildcard](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_selection-by-name-wildcard.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_selection-by-name-wildcard.png)

> Note
> For policies that need to span all applications, use the asterisk wild card by itself.

#### 7.3.2 Application label

You can also use labels to bind a policy to multiple applications. For example, you could protect all applications that use MongoDB or applications that have been annotated with, say, the 'gold' label. Matching occurs on labels applied to namespaces, deployments, and statefulsets. If multiple labels are selected, a union (logical OR) will be performed, matching all applications with at least one of the labels.

Label-based selection can be used to create forward-looking policies, as such policy would automatically apply to any future application with the matching label. For example, if you use a label of 'heritage: Tiller' (for Helm v2) or 'heritage: Helm' (for Helm v3), the selector will apply the policy to any new Helm-deployed applications because the label is applied to any Kubernetes workload created by the Helm package manager.

[![rancher veeam kasten k10 selection by label](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_selection-by-label.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_selection-by-label.png)

#### 7.3.3 Other resources

K10 can also protect cluster-scoped resources without targeting any applications. To specify this, select *None*.

[![rancher veeam kasten k10 selection cluster scoped](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_selection-cluster-scoped.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_selection-cluster-scoped.png)

For more information about protecting cluster-scoped resources, see [Cluster-Scoped Resources](https://docs.kasten.io/latest/usage/clusterscoped.html#clusterscoped).

#### 7.3.4 Customization

You can further customize what is and what is not covered under a K10 application protection policy with:

- [namespace exclusions](https://docs.kasten.io/latest/usage/protect.html#namespace-exclusion)
- [exceptions](https://docs.kasten.io/latest/usage/protect.html#exceptions)
- [resource filtering](https://docs.kasten.io/latest/usage/protect.html#resource-filtering)

## 8 Working with policies
-------------------------------------------------------------------------------------------------------------------------------------------------------------

When you have created a policy and have navigated back to the main dashboard, you will see the selected applications quickly switch from unmanaged to non-compliant. That is, a policy covers the objects but no action has been taken yet. The applications will switch to compliant as snapshots and backups are run and the application enters a protected state. You can also scroll down the page to see the activity, how long each snapshot took, and the generated artifacts.

[![rancher veeam kasten k10 policy activity](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_policy-activity.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_policy-activity.png)

> Note
> More detailed job information can be obtained by clicking the in-progress or completed jobs.

### 8.1 Manual policy runs

It is possible to manually run a policy by clicking the *run once* button on the desired policy. Any artifacts created by this action will not be eligible for automatic retirement and will need to be manually cleaned up.

[![rancher veeam kasten k10 policy detail manual](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_policy-detail-manual.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_policy-detail-manual.png)

### 8.2 Restoring existing applications

Restoring an application is accomplished via the *Applications* page. To restore an application, simply click the *restore* icon in the application’s card.

[![rancher veeam kasten k10 restore app1](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_restore-app1.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_restore-app1.png)

> Note
> While the UI uses the "export" term for backups, no import policy is needed to restore from a backup. Import policies are only needed when you want to restore the application into a different cluster.

Next, you can select a restore point. These are distinguished in the UI as having been generated manually or automatically through a backup policy.

[![rancher veeam kasten k10 restore app2](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_restore-app2.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_restore-app2.png)
A restore point may include snapshots (native to the cluster) and backups (exported outside the cluster) with the same data. When both snapshots and backups are present, the UI provides you the option to select the instance you want to use to restore the application.

[![rancher veeam kasten k10 restore app3](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_restore-app3.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_restore-app3.png)
Selecting a restore point will bring up a side-panel containing more details on the restore point for you to preview before you initiate an application restore.

[![rancher veeam kasten k10 restore app4](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_restore-app4.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_restore-app4.png)

When you click *Restore*, the system automatically re-creates the entire application stack into the selected namespace. This not only includes data associated with the original application but also the versioned container images.

> Note
> Restored PersistentVolumes may not have the annotations specified in the original PersistentVolume.

After the restore completes, you can go back to your application and verify that the state was restored to what existed at the time the restore point was obtained.

### 8.3 Restoring deleted applications

Restoring a deleted application follows nearly the same process, except that removed applications are not shown on the Applications page by default. To discover them, you simply need to filter and select *Removed*.

[![rancher veeam kasten k10 restore deleted app](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_restore-deleted-app.png)](https://documentation.suse.com/en-us/trd/kubernetes/single-html/kubernetes_gs_rancher_veeam-kasten/images/rancher_veeam-kasten_k10_restore-deleted-app.png)

When the filter is in effect, you see applications that K10 has previously protected but which no longer exist. These can now be restored using the normal restore workflow.

## 9 Summary
---------------------------------------------------------------------------------------------------------------------------------

SUSE Rancher enables enterprises to streamline multi-cluster Kubernetes operations everywhere with unified security, policy, and user management. Kasten K10 by Veeam delivers easy-to-use, Kubernetes native application backup and restore, disaster recovery, and application mobility. Together, SUSE and Veeam provide enterprises with the tools they need to reduce risk and accelerate cloud native success.

In this guide, you learned how to seamlessly deploy Kasten K10 into your Rancher Kubernetes landscape, create policy-driven backups, and restore applications.

Continue your journey with these additional resources:

- ["Cloud native workload protection with Kasten K10 by Veeam and SUSE Rancher" - demonstration video](https://youtu.be/c_mSNy6Q9RU)
- ["Kasten K10 by Veeam and SUSE Rancher: Enterprise K8s data protection" - blog article](https://www.suse.com/c/kasten-k10-by-veeam-and-suse-rancher-enterprise-k8s-data-protection/)
- ["Deploying Multicluster Day 2 Operations with SUSE Rancher, Fleet, and Kasten K10" - blog article](https://www.suse.com/c/deploying-multicluster-day-2-operations-with-suse-rancher-fleet-and-kasten-k10/)
- [Get started with SUSE Rancher in 2 easy steps](https://www.suse.com/products/suse-rancher/get-started/)
- [Download Kasten K10 for free and use it for up to 5 nodes](https://www.kasten.io/free-kubernetes)
- [Kasten K10 Documentation](https://docs.kasten.io/latest/index.html)
- [Rancher Documentation](https://rancher.com/docs/rancher/v2.x/en/)

