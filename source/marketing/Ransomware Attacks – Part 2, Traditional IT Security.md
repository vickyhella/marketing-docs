# Ransomware Attacks – Part 2, Traditional IT Security

## Introduction

In this article, the second part of this series dedicated to ransomware, I am going to explore how we can protect traditional IT infrastructure.

## How do we protect traditional IT infrastructure from ransomware attacks?

Although there are many companies moving their workloads to containers and cloud native applications, we cannot forget [the majority of workloads still runs on traditional IT environments](https://www.cncf.io/reports/cncf-annual-survey-2022/). Therefore, is very important to ensure that it is secure and up-to-date.

The operating system (OS) layer is key here since it provides the majority of libraries applications use. Consequently, it’s very important that these contain no known vulnerabilities and come from reliable sources.

This is where SUSE can help. SUSE is a leader in providing [secure, reliable, and high-performance IT infrastructure solutions](https://www.suse.com/solutions/security/), including SUSE Linux Enterprise Server ([SLES](https://www.suse.com/products/server/))

Linux distributions have many security features and tools that can be used to secure your application, such as [SELinux](https://documentation.suse.com/sles/html/SLES-all/cha-selinux.html#sec-selinux-why), [AppArmor](https://documentation.suse.com/sled/html/SLED-all/cha-apparmor-intro.html) and [Netfilter](https://documentation.suse.com/sles/html/SLES-all/cha-security-firewall.html).

These are also used by container management platforms such as [Kubernetes](https://www.suse.com/suse-defines/definition/kubernetes/), SUSE provides these and other tools to ensure system security and reliability on traditional IT infrastructure, and places special focus and passion on working with security agencies to produce configuration guides and certify its products to help users to secure their platforms.

[Here is a list of the SUSE security certifications](https://www.suse.com/support/security/certifications/), amongst them we should highlight the Common Criteria EAL4+ which is a strong indicator that the software supply chain is secure. At the time of writing this article SLES 15 is the only general-purpose Linux OS awarded with this certification.

## How do I use SELinux, AppArmor and Netfilter to protect against ransomware?

SELinux and AppArmor can be used to protect processes from accessing files they shouldn’t and behaving unexpectedly. This limits the spread of malware if the system is infected or the attackers are trying to exploit a vulnerability in the application protected by them.

Netfilter is the firewall of the Linux kernel, it is a very versatile and powerful tool for protecting applications from unwanted access through networks.

These security tools are themselves pretty complex and deserve a dedicated article themselves. However, when configured correctly they can be used to protect against ransomware and/or stop its spread, provide a multi-layered defense, and allow for a Zero-Trust approach to security in traditional IT infrastructure.

## Can [STIG hardening guide](https://www.suse.com/c/applying-disa-stig-hardening-to-sles-installations/) help me protect my Linux server from a Ransomware attack?

The Security Technical Implementation Guides (STIGs) are a set of guidelines and procedures for securing information systems and networks. Developed by the U.S. Defense Information Systems Agency (DISA), they outline the requirements for systems when connected to a U.S. Department of Defense network. Working closely with DISA, SUSE has developed an implementation guide for SLES 15.

Needless to say, [STIG guidelines](https://public.cyber.mil/stigs/) are useful to anybody looking to harden a Linux server and make it less vulnerable to attacks, including ransomware.

The STIGs provide a detailed list of security settings and configuration options that should be implemented on a Linux server, including:

- Use of strong passwords and account lockout policies

- Restricting access to the server to only authorized users and hosts

- Configuring the firewall to block unnecessary incoming and outgoing traffic

- Disabling unnecessary services and daemons

- Restricting access to the server to only authorized users and hosts

- Enabling logging and monitoring of system events

- Enabling regular software updates

- Etc.

Implementing these guidelines can help to reduce the attack surface of a Linux server and make it less vulnerable to common attack vectors, including ransomware.

SUSE provides ways to apply part of this rules automatically for more detailed information I recommend to read Marcus’s blog post “[Applying DISA STIG hardening to SLES installations](https://www.suse.com/c/applying-disa-stig-hardening-to-sles-installations/)&#8220;.

## How can I apply a STIG profiles to all my servers with openSCAP and SUSE Manager?

One of the challenges we face when securing our infrastructure is how to manage security at scale. Patching or configuring hundreds, or even thousands, of servers is a very time consuming task and is prone to errors if done manually, during this time our systems can vulnerable to attackers.

[SUSE Manager](https://www.suse.com/products/suse-manager/) (SUMA) is a server management solution from SUSE that provides comprehensive systems management capabilities for Linux-based systems, including those running on SUSE Linux Enterprise Server (SLES), all through a web-based interface or via API calls. It enables IT administrators to easily manage and monitor their physical and virtual servers, as well as their software packages, patches, and configurations. SUMA also provides support for [openSCAP](https://documentation.suse.com/suma/en/suse-manager/administration/openscap.html), an open-source tool which allows IT administrators to apply security profiles to their servers to ensure compliance with security standards like STIG as well as generate reports on the system’s compliance with the policies.

With SUSE Manager and openSCAP, we can apply STIG profiles to multiple servers at the same time, saving time and efforts as well as allowing you to observe from a single pane of glass all the servers that are secured and those who need attention. 

We can see here the general steps to apply a STIG profile to a Linux server [using openSCAP and SUSE Manager](https://www.suse.com/c/suse-manager-and-openscap-200-security-rules-made-for-you/):

1. Install [openSCAP](https://documentation.suse.com/suma/en/suse-manager/administration/openscap.html) and [SUSE Manager](https://documentation.suse.com/suma/en/suse-manager/installation-and-upgrade/installation-and-upgrade-overview.html) on the server.

2. Download the STIG profile that you wish to apply from the [DISA website](https://public.cyber.mil/stigs/).

3. Import the STIG profile into SUSE Manager.

These steps only need to be done the first time.

4. Use SUSE Manager to apply the STIG profile to the server.

5. Use openSCAP to scan the server and evaluate it against the STIG profile.

6. Review the openSCAP report to see which security settings and configurations are compliant and which are not.

7. Use SUSE Manager to make any necessary changes to the server's security settings and configurations to bring them into compliance with the STIG profile.

8. Repeat steps 5-7 as needed to maintain compliance with the STIG profile.

It's important to note that applying STIGs is not a one-time event, but it is an ongoing process. It's important to regularly monitor the systems to ensure they are still in compliance with the STIGs and otherwise make any necessary changes.

## Are there any other tools that I can leverage to build my Ransomware protection strategy?

SUSE Linux Enterprise Server (SLES) provides more tools that can help you to build your protection strategy against ransomware.

- AIDE (Advanced Intrusion Detection Environment) is a tool that can be used to detect unauthorized changes to a Linux server. It works by creating a cryptographic checksum of all the selected files on the server and storing it in a database. This database is then used as a reference point, and AIDE can periodically scan the server to compare the current state of the files to the reference point stored in the database.

If AIDE detects any changes that were not authorized, it will alert the user and provide detailed information about the changes.

This helps as well to create an audit trail of changes that can make the forensic analysis process easier.

It's important to note that AIDE is not a replacement of a full-fledged endpoint protection solution and it's not capable of detecting all types of ransomware. It's also important to regularly update the tool and the rules to detect new variants.

For more information about AIDE here is the [documentation for AIDE configuration on your SLES servers](https://documentation.suse.com/sles/html/SLES-all/cha-aide.html).

- [ClamAV](https://en.opensuse.org/ClamAV), which is an open-source antivirus engine designed for detecting trojans, viruses and other malware, can be used to scan files for malicious content on a shared folder serving Desktop systems, in this way it can reduce the spread of ransomware on Desktop systems, even when those are using other operating systems.

It allows for plugins such as ClamSAP, a plugin that can be used to scan and protect SAP systems from malware. It is specifically designed to integrate with the SAP Netweaver platform and to scan for malware in the files and databases that are used by SAP applications.

Both ClamSAP and ClamAV are bundled with your SUSE Linux Enterprise Server for SAP subscriptions and are updated regularly.

## Summary

*This article provides an overview of some of the tools and services available from SUSE to protect organizations IT services running on traditional IT infrastructure from ransomware and other forms of malware, [SUSE products are designed to prioritize security](https://www.suse.com/support/security/mission/), our team [constantly innovates in the area of security](https://www.suse.com/support/security/) to ensure your business remains stable and resilient.*

*If you want to know more about SLES please feel free to download our [Buyer’s guide](https://more.suse.com/Buyers_Guide_Enterprise_Linux.html).*

For more information about our products and services, please [contact us](https://www.suse.com/contact/).

*For other articles in this series please visit:*

[Ransomware Attacks - Part 1, Introduction](https://www.suse.com/c/ransomware-attacks-part-1-introduction)

[Ransomware Attacks - Part 3, Container Security](https://www.suse.com/c/ransomware-attacks-part-3-kubernetes/)

[How to protect your SAP applications from Ransomware attacks](https://www.suse.com/c/ransomware-attacks-part-4-sap-applications/)