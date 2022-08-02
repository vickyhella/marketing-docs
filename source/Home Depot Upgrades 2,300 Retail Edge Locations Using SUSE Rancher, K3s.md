# Home Depot Upgrades 2,300 Retail Edge Locations Using SUSE Rancher, K3s

For the third year running, [SUSECon](https://susecon.com/) is again virtual this year, making it one of the last of the major tech conferences that hasn’t returned to opening its doors to in-person attendees.  SUSECon 2022, a two-day event that opened Tuesday morning, is hosted by Germany-based SUSE, one of the "big three" enterprise Linux companies – the other two being Red Hat and Canonical.

Like last year, much of the focus at SUSECon 2022 is on cloud native technologies (read "Kubernetes”) and the edge, which isn't surprising, given that in 2020 SUSE reportedly spent something north of [$600 million to acquire Rancher Labs](https://www.datacenterknowledge.com/open-source/rancher-acquisition-may-make-suse-kubernetes-and-hybrid-cloud-powerhouse), which was then a six-year-old Cupertino-based startup built around its popular Rancher Kubernetes management platform.

Post acquisition, Rancher and SUSE engineers have been focused on tightly integrating Rancher products with SUSE's eponymous Linux distribution, in hopes that it will help the company more effectively compete with Red Hat Enterprise Linux and its Kubernetes-focused OpenShift platform.

That $600 million gamble seems to be paying off. At this year's SUSECon, a number of enterprises, including Deutsche Telecom, Hewlett Packard Enterprise, HPC-focused UberCloud, Fujitsu, and others were on hand to sing the praises of the SUSE/Rancher combination.

Also on hand for a brief keynote interview with SUSE's GM of edge, Keith Basil, was Zachary Hardin, director systems engineering at Home Depot, which recently moved all of its more than 2,300 retail locations to a new architecture based on Rancher technology.

Basil recently told Data Center Knowledge that Home Depot was primarily seeking to move away from having to manually maintain the availability of its containerized applications.

"The challenge with Home Depot is that they are a cloud native shop, so they're [containerized all the way](https://www.datacenterknowledge.com/edge-computing/how-kubernetes-could-underpin-edge-computing-platforms) with their in-store applications," Basil recently told Data Center Knowledge. "They very much appreciated the simplicity of K3s as a single binary that you can run and have a CNCF certified Kubernetes distro at your beck and call."

## **K3s and Rancher**

K3s is a minified version of the Kubernetes platform for managing containers that Rancher designed specifically for edge locations such as Home Depot's retail stores, where resources might be limited and trained IT people might not be readily available. The whole platform weighs in at less than 50MB, and only requires 512MB to run (compared to a standard Kubernetes deployment's average 4GB minimum RAM requirement), making it easy to use in single node clusters.

It also doesn't require using etcd datastore, which can be difficult to set up for those without the proper expertise, leaving users free to instead use familiar (and relatively easy) SQL databases like MySQL or PostgreSQL -- or even K3s's embedded SQLite database. There are other data storage options as well, such as K3s’s embedded HA datastore built on top of embedded etcd, which provides a highly available solution in edge locations where the operational overhead for managing databases might not be available.

Perhaps more importantly for edge locations, is the ease-of-use that K3s brings into play. As Sheng Liang, Rancher's co-founder and former CEO, who's now SUSE's president of engineering and innovation, [once told me](https://www.itprotoday.com/containers/rancher-labs-k3s-shrinks-kubernetes-edge), it's "pretty much like a lights-out, hands-off kind of operation."

According to Basil, that's even more true when using K3s with Rancher and Fleet, Rancher's GitOps solution.

"On top of the OS you've got K3s, which is the binding glue that pulls the cluster together," he said. "Once that cluster is aware, that cluster phones home to Rancher, and then you can start deploying applications down to those clusters to the remote stores. If a Git repo exists with your manifest for the applications that are going to run in every store, you can point that cluster to that Git repo, and it will pull down everything needed to run at that store."

"You have another cluster under management at that point," he added.

## **Other Advantages**

Data center maintenance without the need of an IT professional on hand is another advantage of combining K3s, or any other Kubernetes distribution, with Rancher for edge deployments like Home Depot's that includes thousands locations.

Basil pointed out that typically each remote location will have at least a three node cluster, meaning three separate machines in each store (or more than 6,900 machines in Home Depot's case), with each machine needing to be kept up-to-date with security patches, operating system upgrades, and the like.

"We are reusing Kubernetes directives, philosophies, approaches, and best practices to reach further down into the cluster and swap out the operating system in a running cluster, and then bring that machine back into the cluster once we do that upgrade of the OS, and then just go to the next machine," he said. "Nobody else has that capability today, and Rancher is the orchestrator for that entire flow"

Not only can Rancher reach down to swap out the operating system running underneath the container infrastructure without the need for a human on the scene to facilitate the upgrade, it can also easily handle keeping the Kubernetes layer up-to-date.

"If you were to upgrade from Kubernetes 1.23 to 1.24, again you have to do that times 2,300 locations," he said. "The facilities in Rancher allow that to happen with a GitOps approach with Fleet, and can easily easily handle the scale of doing that type of cluster upgrade in a rolling capacity for all those downstream clusters."

Basil pointed out that while none of SUSE's competitors can currently match Rancher's capabilities at scale, this is no time for the company to get complacent.

"Our competitors, obviously, are coming for us," he said.
