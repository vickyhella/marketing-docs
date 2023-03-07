# Ransomware Attacks – Part 1, Introduction

In this series of articles, I will talk about Ransomware attacks and how we can better protect our systems.

## What is ransomware?

Ransomware is a type of malicious software (malware) that is designed to block access to a computer system or its data, usually by encrypting it, until a ransom is paid. It is typically spread through phishing emails, malicious links, social engineering tactics or exploiting vulnerabilities. Once the ransomware is activated, the attacker will demand a payment in exchange for a decryption key, or access to the system. This type of attack is usually financially motivated, as attackers can potentially make large amounts of money from unsuspecting victims.

The attacker aim will be to spread the malware in as many systems as possible before blocking the access in order to maximize the pressure on the victim to pay the ransom, the business may not be able to continue operating properly during this time and may also be threaten with the release of sensitive data.

The consequences can be far reaching, not just impacting the organization affected by it but also its customers as we could see with the [attack on the Royal Mail in the UK that left customers unable to fulfill international orders](https://www.bbc.com/news/business-64291272) or the [recent wave of attacks on unpatched infrastructure](https://cybernews.com/news/global-ransomware-attack-targets-vmware-servers/)

## What are the steps of a ransomware attack?

This will depend on the level of sophistication; the process tends to be automated in most cases but in some, targeting big organizations, criminal groups may spend more time preparing to make sure they can successfully force the organization to pay.

- **Gain access**

A ransomware attack typically begins with the attacker gaining access to a victim's computer or network through methods such as phishing emails, infected software downloads, or exploiting vulnerabilities through the network.

- **Spread**

Once the attacker has access to a system in the internal network, it will try to spread the malware across. For simple attacks the spread will depend on the sophistication of the malware and this will happen automatically, for more targeted attacks the malware will call home and let the attacker research ways to spread further and take control of more systems.

- **Surface and hold hostage**

Once deemed appropriate by the algorithm in case of an automated attack or by the criminal organization, the blocking of systems and encryption of data will start, in most cases a message will be displayed on some of the victim's computers, demanding a ransom payment in exchange to restore access to the data and/or systems.

The ransom payment is typically made in the form of cryptocurrency, such as Bitcoin, Monero, etc… as it allows for anonymity and is difficult to trace. The attackers may also provide a deadline for the payment to be made, and threaten to delete or release the encrypted data and files if the ransom is not paid, this deadline is set so to limit the time of reaction in order to force the victim to take the payment option.

## Are Linux servers vulnerable to Ransomware attacks?

Linux servers are not immune to ransomware attacks, but they are generally considered to be less vulnerable. This is because Linux servers typically have fewer vulnerabilities and are not so commonly targeted by attackers.

Additionally, Linux servers often have built-in security features, such as [AppArmor](https://doc.opensuse.org/documentation/leap/security/html/book-security/cha-apparmor-intro.html) and [SELinux](https://doc.opensuse.org/documentation/leap/security/html/book-security/cha-selinux.html), which can help to prevent malware from executing and spreading.

However, it's important to note that Linux servers can still be vulnerable to attacks if they are not properly configured or if they are running outdated software.

Any attack on the supply chain of the Linux distribution/s in use can lead to infected software being distributed across the systems.

## Are container environments also vulnerable?

Ransomware can be deployed to a container image or container environment just like any other application, and data can also be encrypted or stolen. Additionally, ransomware can spread the underlying host which is running on Linux, and although Containers managed by Kubernetes distributions make the spread of ransomware more complicated, can also complicate the resolution and forensic analysis required.

## What to do?

It's important to note that paying the ransom does not guarantee that victim’s data and systems access will be restored or that sensitive data is not leaked.

In fact, some attackers may not even provide the key, or they may demand additional payments.

According to [Statista only 54 percent of organizations regained access to data or systems following the first payment in 2021](https://www.statista.com/statistics/1147471/outcomes-organizations-ransom-payments-it-professionals/).

Additionally, paying the ransom also encourages the attackers to continue their malicious activities.

Also, the vulnerability still there, another criminal group can exploit it again.

**The best is to prevent**, this can be done by adopting healthy operational and security practices like:

- Keep all software and operating systems **up to date**.

- For desktop systems use **anti-virus and anti-malware software**.

- Regularly **scan for vulnerabilities and security compliance**, the key here is to do it regularly and the best way is to automate it so that it does not become a hassle and can be incorporated as a part of the deployment process.

- Make sure the **software supply chain** is properly **secured**. From an attacker’s perspective attacking the supply chain can be the easiest way to reach the majority if not all of the systems in an organization.

- Implement proactive measures and adopt **Zero-Trust policies**. This applies for both [containers](https://www.suse.com/c/another-orchestrated-attack-how-do-i-protect-myself/) and traditional environments.

- **Adopt best practices for passwords** validation, such as avoiding common words and use long sentences which are easier to remember for humans but difficult to crack by machines.

- **Educate your employees** on basic security principles, to be wary of suspicious emails, how to identify suspicious links, and data management to avoid having critical data stored on places that won’t be backed up.

- **Perform regular backups** and always store a cold backup on a **separate physical location without network access**. Always make sure the restore procedures are periodically tested.

- **Automate your infrastructure deployment**, so you can quickly restore your systems, time is money.

- Have a **Disaster Recovery Plan**, and make sure it’s tested periodically.

## Conclusions

Ransomware attacks can be extremely damaging and very complex to tackle with a very limited timeframe of action. The best way to deal with them is by avoiding them in first place using mechanisms to prevent and mitigate their impact.

[SUSE has a history helping its customers to defend themselves from these  attacks](https://www.suse.com/success/grupo-agora/).

In the following parts of this series will explain in more details some of the listed methods to prevent them.

For other articles in this series please visit: 

[Ransomware attacks - part 2, Traditional IT](https://www.suse.com/c/ransomware-attacks-part-2-traditional-it).

[Ransomware Attacks - Part 3, Container Security](https://www.suse.com/c/ransomware-attacks-part-3-kubernetes/)

[How to protect your SAP applications from Ransomware attacks](https://www.suse.com/c/ransomware-attacks-part-4-sap-applications/)