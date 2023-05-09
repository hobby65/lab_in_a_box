# Creating a Virtual machine

Now the environment is running, it is time to test if we can run a VM.  
We now need a additional component and that is the OS install media as  
iso file.  
Most distributions have one ready to download, take the time to select  
and download the correct iso file and store it on your drive that will  
host the VM's.  

assuming you are ready to proceed:

## Create your fist set of VM's

We create 2 VM's now, the goal is to make one of them a GIT server, the other  
will be a development machine to write code or to do other things. We need   
this one to setup the git machine for us.  

Start virtualbox (if yoou didn't already) and klick on 'Add machine' in the menu.  
A new dialog will open and here you can define the basic settings for you new virtual  
machine. While it is tempting, we will not use 'unattended Installation' as we don't   
have control over the features that will be used for installation.  

### First machine
The first machine to create will be the GIT server, this is our central repository for  
all code, so we want this one first. To define the machine, use the following parameters:

* Name:            The name of the virtual machine (I used 'gitserver')
* Folder:          This should be correct already, if you set the default earlier
* ISO Image:       Select the downloaded iso image for the distribution you want to use
* Type:            Pobably already changed to Linux(autodetected) else change it
* Version:         Select the correct version for the distro
* Skip Unattended: Check this box, so you can install the distro yourself

In the hardware Tab:

* Set memory to 2048 MB (it probably is already)
* Set CPUs to 1
* Do not! enable uefi

In the hard disk Tab:

The defaults will do, 20 GB disk is sufficient for a git server.

Click 'OK' to create the VM  
In the settings of the created VM, still need to adapt the network.  
For the first network adapter, set it to 'Nat Network' and 'natnet1'.  
If you dont do this the machine will not have the correct settings.  

Now 'Start' the VM  
You will see a VM booting from the iso image and from here you have to install the OS.  

Be sure to manually configure the IP address (192.168.15.7) during installation.  
Add the user ansible to the system, with key authentication, so you can run playbooks against  
the system.  

### Second machine
Now we do almost the same thing, but this time be sure to install a X11 desktop  
the other settings will be ok..except of the ip address of course, that should be different  

### Next steps
Next there will be a few tasks for you to make it all work..  

* You will need to setup a ansible user with key authentication between the machines.
* configure both machines in /etc/hosts
* install ansible (if not already installed)
* give the ansible user access to sudo
* install git package
* install python3-psycopg2 package 


For GIT, we use gitea, see the links page for the documentation, the documentation is not the best,  
but it will work. What you must do, is install the packages now that are needed to run gitea in   
the first place, so be sure to install those dependencies now.  

* postgresql v10 or better
* git v2 or better
* kernel-devel (not for git, but for the guest additions of VBox)
* gcc
* make

After the installation has finished, reboot the VM's and remove the iso from the drive.  
Place the VBoxGuestAdditions.iso in the optical drive and install these, it makes life   
a lot easier.

We now have a system ready for gitea installation, follow the manual from the links page  
and install gitea.   
If you want to do this a lot faster.. you can use the playbook in the lab_files to  
deploy the gittea service on a host, this will setup your gitea from start to finish.  

after the playbook finishes successfully, go to http://\<your_git_hostname\>:3000  
and be the first to register and become the admin :-)  

Add users/projects as required in your lab....The power is yours

Next: [Install Ansible Automation Platform](install_aap.md)

Back: [Back to homepage](README.md)
