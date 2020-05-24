---
layout: post
title: Introduction to Docker
categories: Cloud, Docker, Containers
banner: /images/containers_and_docker/header.png
banner_alt: Docker
banner_author: <a href="https://pixabay.com/users/valdasmiskinis-12049839/">ValdasMiskinis</a>
---
<a href="https://www.docker.com/">Docker</a> in the last few years has seriously taken the Software Dev industry by storm and for good reason. This ever popular technology has brought a highly effective and simple way of deploying and sharing applications through the use of Containerisation. The technology is now very much mature and used throughout both small and large organisations and an integral part of build pipelines and Dev Ops engineering. Here I cover a quick introduction, raise the advantages of Docker and why you may want to consider using it if not already.

### What is Docker?
Docker reduces any concern for environment, dependencies, configuration and runtime by packaging your application into a single deployable unit of software. The idea behind this means that this deployable unit can be ran from almost anywhere, whether it is your local machine, a QA or Production environment or your friendly co-worker's machine. Portability is easily one of Docker's biggest advantages and the ability to run your application on any one machine is a very powerful use case for Docker.

### Is this different from a Virtual Machine?
The regular approach to hosting applications is to provision a Virtual Machine (VM), choose an OS, install the software and libraries required on your machine for the setup and then let your service run on top of your host. Historically this has worked fine but it turns out VMs are not all that efficient. VMs take a long time to boot up, they consume a considerable amount of space when accounting for the Guest Operating System and actually when you think about it many of them will have duplicate OS kernels while running on the same machine. The size or footprint of a Virtual Machine is also huge and sharing images is not particularly easy.

Containerisation by comparison and more specifically Docker containers provide an alternative to a VM, by acting as an extra abstraction over the host's Operating System. A typical VM requires a Hypervisor (either physical/ "bare metal" or non-physical) to segregate the filesystem and system resources and then a Guest OS on top. Docker removed the need for a Hypervisor and a Guest OS and instead Docker runs in the background as a Daemon process on your host OS. This process can then instantiate containers (Docker's equivalent to Virtual Machines) by forking new processes that are booted up from pre-defined images.

So how does Docker do this? A Docker Image is Docker's way of bundling together the necessary libraries/dependencies, runtime environment, any environmental configuration and application code into one unit. This is similar to VM images but images in Docker are much more lightweight as they don't require the entire Operating System. A Docker Image provides a simple yet very portable way to package your software that can be deployed to virtually anywhere. Provided Docker is installed that host can run your app. Separately to images, you can think of containers as processes or images at runtime. You can run any number of containers with different images and each will have their separate filesystems while sharing the same kernel. This can only be achieved however if each of the containers are based from the same base kernel. For example Windows Docker containers cannot be ran alongside Unix Docker containers. Compared to VMs this still similarly provides self containment while removing the overheard of a Hypervisor to manage VMs and the requirement of a large separate OS on each VM.

## Key Concepts and Design
Docker has a few key concepts that are worth documenting for reference. You can expect to use the CLI as the primary method for interacting with Docker as it adopts a server-client model with a REST API.

For more background reading into how Docker works lookup <a href="https://www.kernel.org/doc/Documentation/cgroup-v1/cgroups.txt">cgroups</a> and <a href="https://containerd.io/">containerd</a>. cgroups or Control Groups are a means of organising and grouping processes in Linux, addressing  the need to segregate resources and share kernel spaces. containerd is the Open Source project for Docker Engine which was published not too long after it's inception.
<ul>
<li><b>Docker Images</b> - A read-only snapshot of a system, it's dependencies, libraries, software and configuration</li>
<li><b>Docker Container</b> - A running instance of a Docker Image with a dedicated rw space</li>
<li><b>Docker Hub</b> - A public cloud registry provided by Docker for publishing and sharing images</li>
<li><b>Docker Daemon</b> - The background process used by the Docker server to create and maintain images and containers</li>
<li><b>Docker Client/CLI</b> - The command line interface tool used by the client to interact with Docker Server</li>
</ul>

## Key Advantages
<ul>
<li><b>1. Portability: </b>This is a huge benefit. Being able to run your app independently of any environment, your local machine or any server</li>
<li><b>2. Consistency: </b>You can be more confident that if your application runs in one environment it sholid do so on another with little or no intervention. This supports the idea of automation in Continuous Integration development processes</li>
<li><b>3. Sharing: </b>No longer do you need to consider what version of the JDK or Python is being used, whether the web server is installed. Just create a Docker Image and distribute the image to other developers and environments. This is made even easier by Docker Hub, the cloud registry to plil and push Docker Images.</li>
<li><b>4. Efficiency and Performance: </b> Containers share the same OS kernel from the host OS, therefore reducing running costs on capacity needs and CPU cycles if you are running software hypervisor.</li>
<li><b>5. Scalable: </b>Due to the portability factor running exactly the same Docker Image across mlitiple servers is easy. Booting up and bringing down containers is fast and many modern container services like AWS ECS support automatic scaling</li>
<li><b>6. Separation: </b>Much like VMs, Containers still reserve separate file system spaces and run as separate processes</li>
</ul>
