---
title: Building a Homelab - Pt. 1
tags: ["homelab","virtualization","proxmox","vmware","virtualbox"]
categories: ["homelab","cyber101"]
description: "Learn how to build a simple homelab where you can practice different scenarios and test cybersecurity tools and techniques."
author: molware
ShowRelated: true
---

Building a homelab is a great way to gain hands-on experience and refine your understanding of computer systems. It allows you to create a simulated environment where you can practice various scenarios, such as setting up and configuring network devices, deploying and managing virtual machines, and testing different cybersecurity tools and methodolgies. The focus for this post will be on building a mostly virtual homelab environment to help individuals learn how to build and scale environments which is a thought process that translates to the ever-increasing adoption of cloud technology.

### Hardware
---
The term homelab can be a little daunting to some but it doesn't neccesarily mean having a physical server room to be able to hone your skills. Your homelab can simply be your own personal laptop, an old computer you have laying around, or even hosted in a cloud environment(where they own and control the hardware) or multiple. The hardware requirements are pretty loose depending on what you'll be running in your lab but we at least need to make sure that we can run [virtual machines](https://en.wikipedia.org/wiki/Virtual_machine). Fortunately, most modern hardware supports virtualization technology so this shouldn't be an issue for most.

#### Intel Processors
If your computer has an intel processor you need to make sure that your processor has VT-X. You can use this [handy article](https://www.intel.com/content/www/us/en/support/articles/000005486/processors.html) from Intel to check if your CPU supports Intel virtualization Technology.

#### AMD Processors
If your computer has an AMD processor you can head over to the [AMD processor specifications page](https://www.amd.com/en/products/specifications/processors), and search for **'virtualization'** to see if your processor supports virtualization.

#### ARM Processors
ARM is a strange one here as it's mostly prevalent on mobile devices but it is also prominently featued in Apple's M1 processors. I assume most users with an ARM processor will be using an M1 mac. In order to check if you ARM processor supports virtualization you'll need to find out if it is running on ARMv8 architecture. [The M1 mac runs on ARMv8.5](https://en.wikipedia.org/wiki/Apple_M1)

#### Memory Requirements
For an ideal homelab setup we want to make sure that we can run both desktop and server(headless) based virtual machines. Headless virtual machines are typically less memory intensive as they don't have to render a user interface. Windows virtual machines are likely to be our most resource intensive and will require [at least 2GB of memory each](https://support.microsoft.com/en-us/windows/windows-10-system-requirements-6d4e9a79-66bf-7950-467c-795cf0386715). Linux-based desktop environments vary a bit but are generally less resource intensive. Ubuntu desktop claims to need 4GB of memory minimum, but you can probably get away with running it with 2GB of memory. Other Linux distros like Lubuntu only require 512 MB of memory for operation which is a solid alternative for keeping our homelab running smoothly. 

The foundation for our homelab environment will likely be a single Windows desktop environment, a single linux desktop environment, and a single headless linux environment. This can run on 4GB of memory in theory but performance would not be ideal. I suggest at least 8GB of hardware memory for a nice smooth running homelab. I personally run 16GB minimum on my laptops and 32GB on my homelab virtualization server, but I did start out with 4GB and scaled my way up as I expanded.

### Storage Requirements
You should ensure that you have adequate storage for your virtual machines. Each Windows VM should be at least 40 GB in size, but yout Linux virtual machines can likely get away with 20GB. Containers can be pretty small, I tend to start with 10 GB and upscale as needed depending on what applications are running in them.

For a type 2 hypervisor I'd recommend having 500 GB total between your OS and the virtual machines you'd like to store. Obviously, the more storage you have the better. For a type 1 hypervisor 256 GB will work but again the more storage the better, but work with what you have available

### Software
---
Nowadays we have tons of options for virtualization software that will help us to build the foundation for our homelab. These options are generally split into two categories: Type 1 and Type 2 hypervisors.

#### Type 1 Hypervisors
A type 1 hypervisor, often referred to as a bare-metal hypervisor, is a layer of software, like that of an operating system, that allows your virtualized systems to interact directly with your computer's hardware components. 

##### Microsoft Hyper-V
Microsoft's Hyper-V is an interesting Type 1 hypervisor as it can simply be enabled on Windows Server and Windows 10 Pro, Ultimate and Enterprise editions. 

You can enable hyper-v in the Windows control panel via "Windows Features."
!["Hyper-V"](/uploads/1/hyper-v.png)

If you're using a Windows 10 machine, Hyper-V is not a bad option for a performant type 1 hypervisor.

##### Linux KVM

Linux KVM, similar to Windows Hyper-V is a virtualization technology that allows you to turn an underlying Linux OS into a type-1 hypervisor that can be enabled via the operating system. Unfortunately, utilizing KVM on Linux will require some dependencies to be installed, and does not come with a graphical user interface. 

 On your favorite Linux distro use your package manager(apt, yum, dpkg, rpm, dnf etc.) to install KVM and its dependencies.Instructions are dependent upon Linux distro and package manager being used, but see [here](https://ubuntu.com/blog/kvm-hyphervisor) for an Ubuntu exmaple. You can edit KVM configs via the command line or you can install  popular KVM manager, [virt-manager](https://virt-manager.org/).

#### Proxmox
Proxmox is my personal favorite tool to manage my homelab as it's easy-to-use an open source. Proxmox itself is not a hypervisor but a virtuaization administration platform,hosted on Linux debian, that leverages KVM to administer and manage Virtual machines. Since it leverages KVM the VM's created within proxmox run on a type 1 hypervisor platform. Proxmox is installed on a bare metal system just like you would install an Operating System. Proxmox also has built in functionality for managing and administering Linux containters(LXC) which are lightweight linux systems that are significantly less resource intensive than VMs and have unlimited potential in a homelab setup. Proxmox comes packed with a feature-rich web console that allows you to create and administer virtual machines and containers.

If you elect to use Proxmox, make sure you have a disk to install Proxmox on, and a separate disk  to install your virtual machines and containers.

#### ESXi
ESXi is an enterprise grade type-1 hypervisor developed by VmWare. ESXi needs to be installed on a bare metal system just like installing an OS. After installing you must configure ESXi with an IP address and then you'll be able to administer tyour VM's via web console similar to proxmox. VmWare does have a suite of software that integrates with ESXi so it can get confusing but for the free version you should be looking to [download VMware vSphere Hypervisor 8](https://customerconnect.vmware.com/evalcenter?p=free-esxi8), which is different from VSphere(Paid version). Be sure to register for a license, and then apply your license after installing ESXi otherwise you'll be using the default evaluation license which expires after 60 days.

#### Type 2 Hypervisors

Type 2 hypervisors are essentially software applications that run on top of an existing operating system. These hypervisors do not directly interact with a computer systems hardware components, but rahter those hardware components are virtualized by the operatiing system and the hypervisor software. Type 2 hypervisors are typically less-performant than type 1 but are generally easier to manage and configure. I'd suggest type 2 hypervisors to those who aren't familiar with virtual machine our cloud technology solutions.

#### Virtualbox
[Virtualbox](https://www.virtualbox.org/) is an open-source type-2 hypervisor created by Oracle. It's installed on guest operating systems like Windows and Linux and Solaris that allows for the  creation and management of virtual machines. It's feature-rich and user-friendly and a great option for those who want a simple type-2 hypervisor to build their virtual homelab.


#### VmWare
VmWare offers several type-2 hypervisors in addition to their type-1 hypervisor ESXi. [VmWare Workstation Player](https://www.vmware.com/products/workstation-player.html) is the free type-2 hypervisor offered by VmWare that offers features similar to Virtualbox. 

VMware also offers its enterprise-level(paid) type-2 hypervisor [VMWare Workstation Pro](https://www.vmware.com/products/workstation-pro/workstation-pro-evaluation.html) which offers more features than VmWare player and makes it easier to manage and use several virtual machines at a time, something we leverage when experimenting in our homelab.

The VmWare Player equivalent on MacOSX is [VmWare Fusion Player](https://customerconnect.vmware.com/en/evalcenter?p=fusion-player-personal-13), and the Workstation equivalent on is [VmWare Fusion](https://www.vmware.com/products/fusion/fusion-evaluation.html). 

#### Parallels
[Parallels Desktop](https://www.parallels.com/products/desktop/) gets a nod here because it offers a type-2 hypervisor for Macs that also supports virtualization on M1(ARM) macs. This is pretty handy for those with M1 macs that want to be able to build their virtual homelabs. Just be sure to make sure the iso images being used to build your virtual machines support ARM architecture.

Now that we understand what our hardware and software requirements are for our homelab, we can go ahead and try to map out what will actually go into our homelab and how to scale it accordingly.

## [See Part 2 &rarr;](/blog/2-building_a_homelab_2) 
