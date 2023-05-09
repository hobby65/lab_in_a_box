# Create a Repository Server

A repository server is the source for installing packages after the VM  
is created and running, if you want to add additional packages to the   
system, you could mount the dvd and search for them, but with a repository  
server online, just run yum/dnf install...

We want the repository server to host multiple versions of Rhel, so we adapt  
the description on the web to our needs.

## The VM

First we need to create a VM, with the specs as follows:
* CPUs:   1
* Memory: 1024
* Disk:   60GB
* Network: natnet1

## Packages

For a Repository server apache (httpd) needs to be running on the machine to service  
the repositories.

So all we install on top of the base install:

* httpd
* kernel-devel
* gcc
* make

Start the machine, install the OS and configure the network IP address.  
Do not forget to add the VBoxGuestAdditions

## Apache config

The online doc in the links page only configures 1 repository, we want to be able to   
service more then one, so we add an extra directory to the path, just in front of the  
repository itself:  

We don't change the default documentroot in the httpd.conf, we create the following directories:
````
mkdir /var/www/html/rhel8
mkdir /var/www/html/rhel9
````

In those directories, we copy the contents of the respective DVD's we mount on the virtual optical drive.  
As you copy the full content of the DVD, you are copying repositories and there is no need for the  
create repo command.

Do not forget to open the firewall for http traffic...

````
firewall-cmd --add-service=http --permanent
firewall-cmd --reload
````

On the subsequent VM's you can now enable this machine as your local repository server for yum  
Place the following content in the file: /etc/yum.repos.d/local.repo  

````
[BaseOs]
name=BaseOs packages 8.7
metadata_expire=-1
gpg_check=1
cost=500
enabled=1
baseurl=http://reposerver/rhel8/BaseOS/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release

[AppStream]
name=AppStream packages 8.7
metadata_expire=-1
gpg_check=1
cost=500
enabled=1
baseurl=http://reposerver/rhel8/AppStream/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
````

Obviously the above is for a rhel8 installation, adapt settings for rhel9 if needed.  
Your new VM can install packges from the reposerver now.  

Next: [Install Git](create_vm.md)

Back: [Back to homepage](README.md)
