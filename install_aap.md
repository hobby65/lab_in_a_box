# Installing Ansible Automation Platform

Almost there....  
This is the most important part of the 'lab in a box'.. and the most resource   
hungry part... this will cost you the better part of your resources, if you are  
on the minimum sizing stated in the first page.  

Ansible Automation Platform in its smallest installation consists of 2 VM's:

## Ansible Automation Controller
The controller is the heart of your datacenter deployment installation, it runs   
the ansible playbooks to deploy your machines.  
It needs to have the following minimum specs to run/install:  

* memory: 20GB
* CPU's: 4
* storage: 100GB (minimum)

In our setup we need to allocate 2 network adapters for the machine:  
* a 'Nat Network' on 'natnet1' as primary interface  
* a 'Host-Only' adapter 'Virtualbox Host-Only Ethernet Adapter' as secondary interface  

The first adapter is used to communicate with all VM's in the virtual network.  
The second adapter is used for communication with your vbox host to deploy 'vagrant'  
machine's, which we will cover later.  

## Ansible Private Hub 
The private hub is used by the controller to pull collections and execution environments  
that are created by you (custom collection/execution envs) or synced from the community/redhat  
It needs to have the following specs to install/run  

* memory: 8GB
* CPU's: 2
* storage: 100GB
* 1 ethernet adapter on 'natnet1'

## Installation

Take your time to read the installation documentation, this will save you time in the end.  
Be aware that the production version requires a license.   
If you or your organization do not have a license, go awx..  .

## AWX 
AWX has no private hub functionality, so there is more work to be done to set it up.  
As a bonus, it runs in a docker container and doesn't need as much resources it seems.  
I haven't used it, I wanted to stay as close to an enterprise environment as possible.  

## Post installation tasks
After you have installed Ansible Automation Platform, configure a Basic organization, Team  
and user. Setup synchronisation between the private hub and the redhat/community repositories  
Create a requirements.yml file with at least these collections in it:  
````yaml
---
collections:
  - community.general
  - community.vmware
  - community.windows
  - ansible.posix
  - ansible.windows
````
and upload this file to the private hub to have these repositories synced and availlable.

Setup an 'ansible' user and generate a ssh-key pair on the controller.

copy the public key to your windows host machine (ssh-copy-id will not work) and place this  
file as administrators_authorized_keys in the folder: 'C:\ProgramData\ssh\'  

You will now have key based access to you windows host with powershell, and also your automation controller.  

This is one of the most important things to have working now, the rest is ansible configuration and  
playbooks.

Next: [Configure an Inventory](inventory.md)

Back: [Back to homepage](README.md)

