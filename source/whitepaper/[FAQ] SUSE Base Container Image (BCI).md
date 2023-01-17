# BCI FAQ (last reviewed on 2022/04/21)  

## What?  

### What has SUSE announced?  

SUSE Base Container Images (BCI) provides a repository of tested and certified container images based on SUSE Linux Enterprise Server. The container images are ready-to-go for enterprise use. SUSE maintains these images on a regular basis so you can use them worry-free. The images are updated with the latest security patches and features/functionalities are consistent with the base OS releases.  

With Rancher 2.6 SUSE has announced full integration between Rancher and BCI while ensuring the latest security standards.  

### What is the Base Container Image (BCI)?  

SUSE provides several Base Container Images to create applications based on the application requirements.  

SUSE truly open, flexible, secure container images and application development tools for immediate use by developers, integrators, and operators.  

BCI images are available via the [SUSE Container Registry](https://registry.suse.com/) and are free to reproduce, use and distribute, in accordance with the EULA.  

### What does the BCI include?  

BCI includes two sets of container images:  

1. Pure SLE based containers with a minimal set of packages: one with zypper, one without zypper but with rpm and one without both zypper and rpm, adding flexibility to the development environment, removing unnecessary packages making applications faster to deploy and to orchestrate.  

2. Language Stack Container Images with a base environment for programming languages including Python, Node.js, Ruby, .NET, ASP.Net, Java (based on OpenJDK), Go and Rust.  

3. Application Stack Container Images providing ready to use containerized applications like RMT or PostgreSQL.  

### What are the benefits of Base Container Image (BCI)?  

These are the main benefits:  

- Availability: Base Container Images are available on x86-64, arch64, s390x, and ppc64le.
- Security: Enables more secure container images, reducing the number of notifications from container vulnerability scanners.  

### What are the use cases for the Base Container Images (BCI)?  

BCI provides a stable, secure, and open ecosystem where you can develop and deploy applications in a light and flexible environment while leveraging your experience and the stability and security of the SLES (SUSE Linux Enterprise Server) operating system.  

From the different perspectives, BCI offers several opportunities.

- Actual Rancher users:  
    - Enable Rancher to build using stable, reliable, secure, and certified enterprise components.  
    - Leverage SUSE in-house OS knowledge while containerizing applications as the tools will be the same, there is no migration path needed, for instance, from zypper to other packaging systems as the BCI will behave as container base as SLE would do for an OS.  
- Developers:   
    - Free BCI as an option in cases where subscription is a hurdle in cloud-native environments
    - BCI can be deployed in any operating system helping migrations within a multi- vendor ecosystem and avoiding vendor lock-in.  
- ISVs:  
    - ISVs containerizing applications, using stable, reliable, secure, and certified  enterprise OS.
    - ISVs using free Linux to build applications, having no support and no security in  the chain.
    - ISVs who need navigation when containerizing –software, tools, documentation,  consulting.  
    - ISVs (Independent Software Vendors) need to run applications on a variety of  hosts.  
    
### What packages and libraries are available in BCI?  

SUSE provides several BCI allowing developers to choose which one fits their needs, at the same time, they provide developers with notable tools & libraries like compilers, crypto libraries, along with several OS tools, to recap some of them.  

- Package managers and tools like zypper, rpm, sysctl or glibc.  
- Several Libraries: (lib-acl, lib-crypto, lib-openssl, libldap)  

### What legal agreements are needed to build my products on BCI?  

You must accept the default and standard terms and conditions from SUSE Enterprise Linux  

## Why?

### Why did SUSE create Base Container Image (BCI)?  

We want to provide truly open, flexible, and secure container images and application development tools for immediate use by developers and integrators without the lock-in imposed by alternative offerings.  

To match the needs of regulated markets, SUSE plans to provide a specifically hardened and certified SLE-based solution.  

### On which Hardware Platforms will BCI be available? (x86_64, AARM64, POWER, IBM zSeries)?  

BCI are available on x86_64, aarch64, ppcle64 and s390x (.NET images are only available on x86-64 now)  

## How?  

### Do I need a subscription to use BCI?  

No, you can use them without a subscription.  

### Do I need a SUSE Linux environment to build images based on BCI?  

No, you can build and run BCI in any environment that supports building based on OCI  compatible images.  

### Do I need a SUSE Linux environment to deploy BCI?  

No, you can run BCI in any certified Kubernetes deployment or any OCI compatible runtime.  

### Can I freely distribute applications built on BCI?  

There is no restriction to redistribute application based on BCI, images are free to reproduce, use and distribute, redistributable through the EULA.

### Can I distribute my BCI-based container images without using SUSE’s registry?  

If BCI Images are free to produce use and distribute, you can use any registry to distribute your application based on BCI.  

Yes, you can distribute your bci based applications as you want.   

### Can I add non-BCI RPMs to a BCI image and still redistribute the resulting container image on a non-SUSE platform?  

As part of your development process, you can add non BCI-RPMs to the images, as everythingadded on top of the image offered is considered part of the application or dependencies.  

There is no restriction from SUSE to redistribute the result if you comply with the EULA.  

### Is BCI recommended for community projects?  

Yes, absolutely.  

### Will BCI receive updates?  

Yes, we build BCI images out of the SUSE Linux Enterprise Server repository. We build new BCI images for each new SLE (SUSE Linux Enterprise) Service Pack. 

BCI images of a released Service Pack receive updates e.g., in regards of security updates  continuously.   

### How is BCI supported?  

BCI lifecycle, along with the rest of our offering, can be checked in the [SUSE Lifecycle dashboard](suse.com/lifecycle).  

### Will my application built on BCI be supported?  

SUSE supports the available BCI images.  

Applications shipped via the container image should be supported by its vendor or developer.  

### What is the BCI lifecycle?

The General Purpose BCI follows the General Support lifecycle of the SLE Service Pack they are made for. The SUSE Linux Enterprise Server lifecycle can be found [here](suse.com/lifecycle).  

Application and Language Stack BCI has a lifecycle that is tied to the respective application or language stack and not to the respective service pack. For further details, consult the [SUSE lifecycle page](suse.com/lifecycle).  
BCI images are not supported in LTSS (Long Term Service Pack Support).

### How to request new features in BCI?  

SUSE Internally: New feature requests goes through https://jira.suse.com/projects/PM and create a new PM Pool Epic.  

### Does BCI let me distribute my container images anywhere I want?  

Yes, SUSE will never oversee what you do with your images and how you distribute them. BCI are freely distributable, and you can also distribute your applications as you want if you comply with the EULA.  

### Can I add non-BCI packages if something is missing from BCI?  

Yes, but SUSE is supporting BCI as it comes from our registry. Adding packages to BCI is considered part of the development process but is not directly supported by SUSE.
