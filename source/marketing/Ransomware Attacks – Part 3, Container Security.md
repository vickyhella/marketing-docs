# Ransomware Attacks – Part 3, Container Security

## Introduction

In the third part of this series dedicated to ransomware, I am going to explore how we can protect Kubernetes environments.

## How do we protect Kubernetes environments from ransomware attacks?

As containers adoption grows, containerized software is increasingly targeted by ransomware attacks, and the dynamic nature of the Kubernetes environment presents unique challenges. The spread of ransomware can be rapid, potentially infecting the image registry or other parts of the software supply chain and leading to the compromise of all pods in Kuberentes clusters. Attackers may also take control of the Kubernetes clusters, making it difficult to stop the attack once it has started.

As mentioned in the first post of this series, prevention is essential for protecting Kubernetes environments from attacks, which can occur through application vulnerabilities in running software, stolen credentials for the Kubernetes cluster/s, malware planted in the software supply chain, etc.

Some of these potential attack vectors can be only addressed by using specific security software and predefined processes.

To prevent attacks to come through an application vulnerability there are a few strategies we can use:

- **Keep software up to date** with the latest bugfixes, if we want to make sure no vulnerable software gets deploy on the clusters, we can implement admission control rules with [SUSE Neuvector](https://blog.neuvector.com/article/kubernetes-admission-control) which extends the capabilities of Kubernetes by allowing you to define rules that, for example, can prevent an image with vulnerabilities or from a non-trusted registry, from being deployed in your cluster.

- Proactively **block known attack** at the network level before they reach the application, [SUSE NueVector](https://neuvector.com/solutions/application-security-solutions/) is especially suited for this task, as it not only looks at network layer 3 and 4 protocols, which other security solutions focus on, but also at the network layer 7 containing application protocols.

This means it can identify attacks carried over the application layer, thanks to its [Deep Packet Inspection](https://blog.neuvector.com/article/web-application-firewall) (DPI) technology, and it can block these malicious network packets before they reach the application.

- **Limit application privileges.** We can further prevent ransomware attacks by using [Kubernetes security mechanisms](https://kubernetes.io/docs/concepts/security/), such as [Seccomp](https://lwn.net/Articles/656307/), AppArmor and SELinux. As previously discussed in this series, SELinux and AppArmor can be effective in preventing applications from accessing certain files or running certain processes. These are available only on Kubernetes nodes running on top of Linux distributions which have these features enabled, such as [SLES](https://documentation.suse.com/sles/html/SLES-all/cha-selinux.html) and [SLEmicro](https://documentation.suse.com/en-us/sle-micro/html/SLE-Micro-all/cha-selinux-slemicro.html).

## Why use [Zero-Trust](https://www.suse.com/campaigns/zero-trust/) policies to stop the spread of malware?

All these measures may fall short, especially if the attackers use a Zero-Day to gain access.

To protect from [Zero-Days](https://en.wikipedia.org/wiki/Zero-day_(computing)) we need to implement [behavioral-based Zero Trust](https://www.suse.com/c/another-orchestrated-attack-how-do-i-protect-myself/) policies that can observe and block any attempt at deviating from the expected behavior applications show at the network or system levels. This means that even if the attacker gains control of the application, they can't use it to reach other applications in the cluster/s. Usually most backend applications aren't exposed to the outside world and have weaker security protections, some also have greater system privileges, and network or data access thus controlling them would grant the attacker even more opportunities to spread further.

In this scenario we can use SUSE NeuVector’s Zero-Trust security policies because we are looking at the behavior of the application and not only at known attacks. This requires us to define a security policy which may sound a complicated task but, thanks to [NeuVector behavioral learning](https://neuvector.com/why-neuvector/use-cases/security-automation/) capabilities, we can easily create them for each running application, in [this article](https://www.suse.com/c/another-orchestrated-attack-how-do-i-protect-myself/) I show how we can use NeuVector behavioral learning capabilities to create Zero-Trust security policies.

Not only we can use SUSE NeuVector for implementing system and network level [Zero-Trust](https://www.suse.com/c/rancher_blog/zero-trust-the-new-security-model-for-cloud-native-applications-and-infrastructure/) polices, but also to [visualize and detect suspicious activities](https://neuvector.com/solutions/container-visibility-and-monitoring-solutions/) within the Kubernetes environment by looking at the connections that each pod establishes using its DPI technology.

## The importance of having a secure software supply chain in a cloud native environment?

The software supply chain is a critical component of cloud-native environments. It is the end-to-end process that ensures that all applications and components used in a cloud-native environment are secure, from the hardware and OS where Kubernetes runs on to the smallest library used on the end application.

To have it secure we should verify the authenticity of software, for example against [signatures](https://packagehub.suse.com/package-signatures/) from a regulated third party, regularly patching any vulnerabilities, using secure delivery methods and controlling who can modify the code and when, making sure every change is subject to peer review.

This is where certifications such as [SLSA4](https://documentation.suse.com/sbp/server-linux/html/SBP-SLSA4/index.html) and [Common Criteria EAL4+](https://www.suse.com/support/security/certifications/) show their value, ensuring that the SUSE processes for developing and maintain its products, passed the rigorous security evaluation required to be awarded these certifications.

Unfortunately, we cannot always work with certified software, in which case we need to resort to the use of security platforms like [SUSE NeuVector](https://neuvector.com/why-neuvector/use-cases/compliance/), or Kubernetes cluster managers like [Rancher](https://www.rancher.com/products/rancher), to scan the images on the registries for vulnerabilities, validate compliance with security regulations, run security benchmarks on the infrastructure, etc. It is vital that these checks can be repeatable and automated so that they are not a one-off.

## Why must we automate security in Kubernetes environments?

Automating security in Kubernetes environments is essential for ensuring that the environment is secure, compliant and up to date, reducing the attack surface by patching and configuring software resources in the supply chain securely.

Since security in a cloud-native environment is not static, new vulnerabilities and patches appear every day, and criminals automate their attacks to quickly find and attack victims we need to match their methods.

Automation also brings other benefits such as helping to eliminate manual errors and ensuring that security policies are consistently enforced across the environment.

It also can drastically reduce the time it takes to restore a system that has been attacked to its original state. However, it is important to note the system must be patched prior to restoring it; and for that we also need to simplify and automate the updating process.

To make this possible and easier to implement, security platforms like SUSE NeuVector, and Kubernetes management tools like Rancher, are designed with automation in mind. With [Rancher](https://ranchermanager.docs.rancher.com/) we can automatically deploy and upgrade workloads across clusters using [Fleet](https://ranchermanager.docs.rancher.com/v2.6/pages-for-subheaders/fleet-gitops-at-scale) or your own [external CI/CD system](https://www.suse.com/c/rancher_blog/managing-rancher-resources-using-pulumi-as-an-infrastructure-as-code-tool/) which eases the adoption of Infrastructure-as-Code.

With SUSE [NeuVector API and using CRDs](https://blog.neuvector.com/article/kubernetes-policy-as-code-crd) we can load security policies the same way we do with other resources in Kubernetes, making it very easy to implement Security-as-Code using an existing CI/CD platform.

## How can we scale these measures when we have multiple clusters?

We need software that has a low footprint and is able to perform reliably, high resource consumption will impact application performance making it impossible to meet the increased demand, this is one of the key aspects of production ready software.

We can make use of Rancher’s security capabilities I have mentioned earlier to ensure that all Kubernetes clusters are properly configured and secured. Rancher can help with access control, security policy enforcement, and vulnerability scanning.

With SUSE [NeuVector](https://open-docs.neuvector.com/navigation/multicluster) we can also manage the security posture on multi-cluster and multi-cloud deployments by pushing federated rules to each cluster and sync the results of registry scans across multiple clusters.

This approach will enable us to scale our security measures, even when we have multiple clusters, and NeuVector architecture, which works completely without side cards or similar, makes it easy to scale protection along side the application workloads

## Summary

We have seen that having a secure software supply chain and implementing behavioral-based Zero-Trust security policies at the system and network level can help protect against malicious attacks, such as ransomware, and Zero Day threats used by criminals. *SUSE products are designed to prioritize security and work at scale, our team [constantly innovates in the area of security](https://www.suse.com/support/security/) to ensure your business remains stable and resilient.*

*If you want to learn more about Zero-Trust you can download our free [Zero Trust Container Security for Dummies](https://more.suse.com/zero-trust-security-for-dummies.html) ebook, or please feel free to request a [demo of NeuVector](https://more.suse.com/neuvector-security-demo).*

*For more information about our products and services, please [contact us](https://www.suse.com/contact/).*

*For other articles in this series please visit:*

[Ransomware Attacks - Part 1, Introduction](https://www.suse.com/c/ransomware-attacks-part-1-introduction)

[Ransomware Attacks - Part 2, Traditional IT Security](https://www.suse.com/c/ransomware-attacks-part-2-traditional-it)

[How to protect your SAP applications from Ransomware attacks](https://www.suse.com/c/ransomware-attacks-part-4-sap-applications/)