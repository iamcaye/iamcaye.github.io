---
layout:	post
title:	Understanding Linux Virtualization
date:   2021-11-14 18:49:33 +0100
categories: research
---

<p>Virtualization is basically, creating virtualized resources and mapping them to physical resources and it can be done by specific hardware functionality or software functionality. This can be used, for example, to create a virtual machine on your computer, so you have two machines running in the same hardware. </p>
<p>This technology is getting more popular now, but it's not something new or revolutionary, it came from 1967 when IBM created the IBM CP-40 and its S/360-40. But in 2004, with all the new technologies such as PCI Express, DDR2 and multi-core processors, took off and 15 years of development later it got polished enough to attract all the big companies and make them compete for the best virtualization technology.</p>

<h1>The competitive market of virtualization</h1>
<ul>
	<li>Red Hat (KVM)</li>
	<li>Microsoft (Hyper-V)</li>
	<li>VMWare (ESXi)</li>
	<li>Oracle (Oracle VM)</li>
	<li>Google</li>
</ul>

<h2>Types of Virtualization</h2>
<ul>
	<li><b>Desktop Virtualization (VDI - Virtual Desktop Infraestructure)</b></li>
	<p>
		Is a method of simulating a user workstation so it can be accesed from a remotely connected device. The pros of this type is that include easier, centralized managment and monitoring, much more simplified update workflows, simplified deployment processes and easier management of compliance and security-related options.
	</p>

	<li><b>Server Virtualization</b></li>
	<p>
		Is a software architecture that allows more than one server operating system to run as a guest on a given physical server host. I think this type is the most used one right now because it´s very usefull to the companies that need a lot of servers to carry out more tasks. They are easier to backup and more energy efficient than physical ones.
	</p>

	<li><b>Application Virtualization</b></li>
	<p>
		Is a software technology that encapsulates computer programs from the underlying OS on which they are executed. It reminds me of containers and microservices.
	</p>

	<li><b>Network Virtualization (SDN - Software-Defined Networking)</b></li>
	<p>
		This is a technology that creates virtual networks that are independent of the physical networking devices, such as switches.
	</p>

	<li><b>Storage Virtualization (SDS - Software-Defined Storage)</b></li>
	<p>
		Is a technology that creates virtual storage devices out of pooled, physical storage devices that we can centrally manage as a single storage device.
	</p>
</ul>

<h2>Ways to make VMs</h2>
<ul>
	<li><b>Partitioning</b></li>
	<p>
		The CPU is divided into different parts and each part works as an individual system.
	</p>

	<li><b>Full Virtualization</b></li>
	<p>
		A virtual machine is used to simulate regular hardware while not being aware of the fact that it's virtualized.
	</p>

	<li><b>Software-based</b></li>
	<p>
		Uses binary translation to virtualize the execution of sensitive instruction set while emulating hardware using hardware. Trash...
	</p>

	<li><b>Hardware-based (KVM hypervisors)</b></li>
	<p>
		Removes the binary translation from the equation while interfacing with a CPU's virtualization features, which means that instruction sets are being executed directly on the host CPU.
	</p>

	<li><b>Paravirtualization</b></li>
	<p>The hypervisor has an API and the OS of the VM makes calls to it. The host OS has to be modified to receive the calls from that API (hypercalls)</p>

	<li><b>Hybrid Virtualization</b></li>
	<p>Full virtualization + Paravirtualization</p>

	<li><b>Hardware-based (KVM hypervisors)</b></li>
	<p> 
		It's a type of application virtualization that uses containers... told you ;)
	</p>
</ul>
<small>
		*A container is a object that packages an application and all its dependencies so that the application can be scaled out and rapidly deployed without needing a VM or a hypervisor.*
</small>

<h2> Hypervisor/VMM </h2>
<p>
	The VMM (Virtual Machine Manager) or hypervisor is a software that monitores and controls virtual machines or guest OSes. It's responsible for ensuring different virtualization tasks, for efficiently controlling physical platform resources and for allocating the resources requested by these guest OSes.
</p>
<h1>Types of hypervisors</h1>
<p>
There are two types of hypervisors but its definitions are not clear yet. The main difference between this two is that, type 1 ones runs the VMs directly on top of the hardware, unlike the type 2 ones that runs on top of a present OS.
</p>
<ul>
	<li><b>Type 1 Hypervisor</b></li>
	<p> These are also called bare-metal, embedded, or native hypervisors. Their main advantages are that they are easy to install and configure, small in size and more secure</p>

	<li><b>Type 2 Hypervisor</b></li>
	<p>They reside on top of the OS so they have better customization and wider range of hardware support</p>
</ul>
