+++
title = ' The Dockerfile'
date = 2023-11-28T11:27:25+02:00
draft = true
author = 'Ricardo Simba'
+++

Lately, I've been leveraging a lot containers for production, tests, and learning, and I decided to write about it.

# Introduction
On my first job, we had different servers supporting the infrastructure services (DHCP, DNS, FTP, etc) required to provide the Internet service to several customers. Those servers ran Linux and this was before the virtualization technology had matured as it is today, and this means that you had one physical server with a Linux distro installed, and possibly one or more services run on it.

 Virtualization
When it comes to computing, virtualization is a technology that creates a virtual representation of the physical components necessary for a software to install and run successfully. Let's, for example, say that we want to install Linux Debian (more exactly Debian...) on a machine; as per Debian, a machine where Debian need to be installed needs to at least have the following physical resources for it to successfully install and run:
- CPU
- Memory
- Disk

Now, the virtualization technology allows you to virtualization the resources above and still allow the OS to run because it doesnt care

This is means that most of the IT components such as Desktops, Servers, Storage, and Networks can be represented virtually through software. As the virtualization matured, environments described in the first paragraph can be optimized by deploying at least two physical servers, have a hypervisor installed on both servers and install different Linux Virtual Machines and then install the required services on the VMs.

While the virtualization technology brings a lot of advantages, there are use cases where they are still not enough. For example, a server OS requires a more physical and virtual resources and if you a need of running a single application on it, then it means you might have a invested on expensive hardware to run a single application. This is where the container technology comes into play.

# What's a Container

# Container Platforms

# Docker

## Docker Images

## Docker Hub

# Conclusion

