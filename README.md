# Lab in a Box 

## Introduction
This documentation will explain how to setup a lab environment for ansible development  
on a single computer with Oracle virtualbox as host for the complete environment.  
In this documentation we discuss an environment that matches an Enterprise environment   
as close as possible.

My goal with this setup was to be fully self contained, no need for external connections  
to develop, test and deploy a linux platform. Its your choice to use an external git, like   
GitHub or a corporate GIT implementation, but your will be dependant of a network connection  
for this. I wanted to be able to work during commutes, where there is no internet connection  
availlable.

As a deployment with ansible enviroment depends heavily on DNS, and we haven't implemented  
one, we have to configure all hosts in the /etc/hosts file. In the infrastructure_vbox code,  
The newly deployed hosts will be added to the deployment server. For the other machines, you  
should create a role to accomplish this.

I am assuming here, that you have some knowledge of linux and the distribution you want to  
use. Not everything will be explained 'to the bone'....  
For more detail, RTFM... its in the links page

You should be comfortable working with a git implementation.

Be aware that most of the components discussed here are licensed, for most of these there are  
free alternatives, which we will mention, but not discuss, except for the repository server  
which can act as an alternative for satellite.   
If You want to use those alternatives, there is lots of documentation out there...  
[Links Page](links.md)

## Hardware requirements (minimum):

Creating a Redhat Enterpise environment in a box is resource intensive, so expect to spent  
some money in buying hardware for this.

Your system needs to meet the following requirements:
* 64GB memory  (more is better)
* 1 TB SSD dedicated disk (again, more is better)
* 4 core CPU 
* virtualization support


## Software requirements:

In this documentation we will run on Windows, but this can be setup on Linux as well. 

Required software:
* Windows 10 (Home) or better
* Oracle VirtualBox 7.0.6 or better
* Oracle Virtualbox Extension Pack v7.0.6 or better (equal to Virtualbox)
* HashiCorp Vagrant 2.3.4 or better

## Next Steps:

Based on the assumption that your system meets the above requirements, the next steps will be:

* [Preparing windows](prepare_windows.md)
* [Configuring Virtualbox](virtualbox_config.md)
* [Installing Repository server](reposerver.md)
* [Create Your GIT server](create_vm.md)
* [Installing Automation Platform](install_aap.md)
* [Inventory setup](inventory.md)
* [Creating rhel box files](create_box_template.md)
* [Install satellite (optional)](install_satellite.md)

## Project deployment ansible code

Included in this repository, the code needed to automaticly deploy machines is added.  
Look for the labfiles.tgz file, it is a tar archive with 4 git repositries, one play  
three roles...  
An additional play is also included to configure gitea on a host.  
[Download link](labfiles.tgz?raw=1)

This code can deploy your VM's on your host using vagrant and the inventory.


## License
TBD

## Author
Wilco Folkers
