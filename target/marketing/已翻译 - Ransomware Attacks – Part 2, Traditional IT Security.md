# 勒索软件攻击 Part 2：传统 IT 安全

## 介绍

本文是勒索软件攻击系列的第二篇文章，在本文中，我将探讨如何保护传统 IT 基础设施。

## 如何让传统 IT 基础设施免受勒索软件攻击？

目前，很多公司已经将工作负载转移到容器和云原生应用程序，但[大部分工作负载仍运行在传统 IT 环境中](https://www.cncf.io/reports/cncf-annual-survey-2022/)。因此，确保它们安全以及保持最新是非常重要的。

操作系统 (OS) 层是关键，因为它提供了应用程序使用的大多数库。因此，使用不包含已知漏洞并且可靠的源非常重要。

这是 SUSE 可以提供帮助的地方。SUSE 是提供[安全、可靠和高性能 IT 基础设施解决方案](https://www.suse.com/solutions/security/)（包括 SUSE Linux Enterprise Server，简称 [SLES](https://www.suse.com/products/server/)）的领导者。

Linux 发行版有很多用于保护应用程序的安全功能和工具，例如 [SELinux](https://documentation.suse.com/sles/html/SLES-all/cha-selinux.html#sec-selinux-why)、[AppArmor](https://documentation.suse.com/sled/html/SLED-all/cha-apparmor-intro.html) 和 [Netfilter](https://documentation.suse.com/sles/html/SLES-all/cha-security-firewall.html)。

[Kubernetes](https://www.suse.com/suse-defines/definition/kubernetes/) 等容器管理平台也使用这些工具，SUSE 提供这些工具和其他工具来确保传统 IT 基础架构上的系统安全性和可靠性，SUSE 还致力于与安全机构合作生成配置指导和认证自家产品，帮助用户加固他们的平台。

SUSE 安全认证列表[在这里](https://www.suse.com/support/security/certifications/)，其中我们需要重点关注 Common Criteria EAL4+，因为这是软件供应链安全的有力指标。在撰写本文时，SLES 15 是唯一获得此认证的通用 Linux 操作系统。

## 如何使用 SELinux、AppArmor 和 Netfilter 来抵御勒索软件？

SELinux 和 AppArmor 可用于防止进程访问它们不应该访问的文件和意外行为。在受保护的应用程序中，如果系统被感染或攻击者试图攻击漏洞，恶意软件的传播会被限制。

Netfilter 是 Linux 内核的防火墙，是非常通用且功能强大的工具，用于避免应用程序受到网络上不必要的访问。

这些安全工具本身非常复杂，值得专门写一篇文章。如果配置正确，它们可用于抵御勒索软件和阻止其传播，提供多层防御，并允许在传统 IT 基础设施中采用零信任安全方法。

## [STIG 强化指南](https://www.suse.com/c/applying-disa-stig-hardening-to-sles-installations/)可以保护 Linux 服务器免受勒索软件攻击吗？

安全技术实施指南 (Security Technical Implementation Guide，STIG) 是一组用于保护信息系统和网络的指南和程序。

[STIG 指南](https://public.cyber.mil/stigs/)对于希望强化 Linux 服务器并使其免受攻击（包括勒索软件）的任何用户都非常有用。

STIG 提供了应该在 Linux 服务器上实施的安全设置和配置选项，包括：

- 使用强密码和账号锁定策略

- 仅允许授权用户和主机访问服务器

- 配置防火墙来阻止不必要的传入和传出流量

- 禁用不必要的服务和守护进程

- 启用系统事件的日志记录和监控

- 启用定期软件更新

- 其他

实施这些准则有助于减少 Linux 服务器的攻击面，并使其不易受到常见的攻击（包括勒索软件）。

SUSE 提供了自动应用部分规则的方法。有关更多详细信息，建议阅读 Marcus 的博客文章：[在 SLES 安装中应用 DISA STIG](https://www.suse.com/c/applying-disa-stig-hardening-to-sles-installations/)。

## 如何使用 openSCAP 和 SUSE Manager 将 STIG 配置文件应用到所有服务器？

在保护基础设施时，我们面临的另一个挑战是如何大规模管理安全性。修补或配置数百甚至数千台服务器是一项非常耗时的任务，如果手动进行的话很容易出错，而且在此期间系统很容易受到攻击。

[SUSE Manager](https://www.suse.com/products/suse-manager/) (SUMA) 是 SUSE 的服务器管理解决方案，它通过基于 Web 界面或 API 调用为基于 Linux 的系统（包括在 SLES 上运行的系统）提供全面的系统管理功能。它使 IT 管理员能够轻松地管理和监控物理和虚拟服务器、软件包、补丁和配置。SUMA 还提供对开源工具 [openSCAP](https://documentation.suse.com/suma/en/suse-manager/administration/openscap.html) 的支持，让 IT 管理员能将安全配置文件应用到服务器，确保符合 STIG 等安全标准，并生成系统策略合规的报告。

借助 SUSE Manager 和 openSCAP，你可以将 STIG 配置文件同时应用到多台服务器，节省更多时间和精力，而且能从单一管理平台观测所有受保护的服务器和需要关注的服务器。

以下是[使用 openSCAP 和 SUSE Manager](https://www.suse.com/c/suse-manager-and-openscap-200-security-rules-made-for-you/) 将 STIG 配置文件应用到 Linux 服务器的一般步骤：

1. 在服务器上安装 [openSCAP](https://documentation.suse.com/suma/en/suse-manager/administration/openscap.html) 和 [SUSE Manager](https://documentation.suse.com/suma/en/suse-manager/installation-and-upgrade/installation-and-upgrade-overview.html)。

2. 从 [DISA 网站](https://public.cyber.mil/stigs/)下载要应用的 STIG 配置文件。

3. 将 STIG 配置文件导入 SUSE Manager。

这些步骤只需要在首次操作时进行。

4. 使用 SUSE Manager 将 STIG 配置文件应用到服务器。

5. 使用 openSCAP 扫描服务器，并根据 STIG 配置文件进行评估。

6. 查看 openSCAP 报告，了解合规和不合规的安全设置和配置。

7. 使用 SUSE Manager 对服务器的安全设置和配置进行更改，使其符合 STIG 配置文件。

8. 根据需要重复步骤 5-7，使其保持符合 STIG 配置文件。

请务必注意，应用 STIG 不是一劳永逸的，需要持续执行操作。你需要定期监控系统来确保它们仍然符合 STIG，如有需要则进行更改。

## 有没有其他工具可以用来构建勒索软件保护策略？

SUSE Linux Enterprise Server (SLES) 提供了更多工具，可帮助你构建抵御勒索软件的保护策略。

- AIDE（Advanced Intrusion Detection Environment）是一个工具，用于检测对 Linux 服务器进行的未经授权更改。它会创建服务器上所有选定文件的加密校验和，并将其存储在数据库中。该数据库后续将用作参考点，AIDE 可以定期扫描服务器，并将文件的当前状态与存储在数据库中的参考点进行比较。

如果 AIDE 检测到任何未经授权的更改，它将提醒用户并提供更改的详细信息。

这也有助于创建更改的审计线索跟踪，使取证分析更加容易。

请注意，AIDE 不能替代成熟的端点保护解决方案，它无法检测所有类型的勒索软件。定期更新用于检测新变体的工具和规则也很重要。

有关 AIDE 的更多信息，请参阅 [SLES 服务器上的 AIDE 配置文档](https://documentation.suse.com/sles/html/SLES-all/cha-aide.html)。

- [ClamAV](https://en.opensuse.org/ClamAV) 是一个开源防病毒引擎，专为检测木马、病毒和其他恶意软件而设计，可通过扫描桌面系统上共享文件夹中的文件来查找恶意内容，减少勒索软件在桌面系统（即使使用的是其他操作系统）上的传播。

它允许用户使用 ClamSAP 之类的插件，该插件可用于扫描和保护 SAP 系统，让其免受恶意软件的攻击。它专门用于集成 SAP Netweaver 平台，并扫描 SAP 应用程序使用的文件和数据库中的恶意软件。

ClamSAP 和 ClamAV 都与 SUSE Linux Enterprise Server for SAP 订阅捆绑在一起，并且会定期更新。

## 摘要

*本文概述了 SUSE 提供的工具和服务，用于让运行在传统 IT 基础设施上的 IT 服务免受勒索软件和其他恶意软件的攻击。[SUSE 产品优先考虑安全性](https://www.suse.com/support/security/mission/)，我们的团队[在安全领域不断创新](https://www.suse.com/support/security/)，确保你的业务保持稳定*。

*如需了解 SLES 的更多信息，请随时下载我们的[购买者指南](https://more.suse.com/Buyers_Guide_Enterprise_Linux.html)*。

如需进一步了解我们产品和服务，请[联系我们](https://www.suse.com/contact/)。

*如需阅读本系列的其他文章，请访问：*

[勒索软件攻击 Part 1：简介](https://www.suse.com/c/ransomware-attacks-part-1-introduction)

[勒索软件攻击 Part 3：容器安全](https://www.suse.com/c/ransomware-attacks-part-3-kubernetes/)

[如何避免 SAP 应用程序受到勒索软件攻击](https://www.suse.com/c/ransomware-attacks-part-4-sap-applications/)