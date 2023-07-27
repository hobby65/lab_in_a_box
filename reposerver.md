# Create a Repository Server

A repository server is the source for installing packages after the VM  
is created and running, if you want to add additional packages to the   
system, you could mount the dvd and search for them, but with a repository  
server online, just run yum/dnf install...

We want the repository server to host multiple versions of Rhel, so we adapt  
the description on the web to our needs.

This host will double as DNS server for our lab.

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
* named

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

## DNS config

The named package ensures that the named software is installed.
Now we have to configure the domain to make it run.

We create the following files in the /var/named folder
localdomain.forward
localdomain.rev

The files have the following content:

**localdomain.forward**
```text
$TTL 3600
@ SOA reposerver.localdomain. root.localdomain. (
                                        4      ;Serial 
                                        15m    ;Refresh
                                        5m     ;Retry
                                        30d    ;Expire 
                                        1h     ;Minimum TTL
)
    NS reposerver.localdomain.
    A 192.168.15.20

host1			IN 	A 	192.168.15.10
host2 		IN 	A 	192.168.15.11
reposerver		IN 	A 	192.168.15.20
gitserver		IN 	A 	192.168.15.21
privatehub		IN 	A 	192.168.15.22
...
```

**localdomain.rev**
```text
$TTL 86400
@ IN SOA reposerver.localdomain. root.localdomain. (
                                            2020011801 ;Serial
                                            3600 ;Refresh
                                            1800 ;Retry
                                            604800 ;Expire
                                            86400 ;Minimum TTL
)
;Name Server Information
@ IN NS reposerver.localdomain.
reposerver     IN      A       192.168.15.20

;Reverse lookup for Name Server
20 IN PTR reposerver.localdomain.

;PTR Record IP address to Hostname
10      IN      PTR     host1.localdomain.
11      IN      PTR     host2.localdomain.
20	    IN	    PTR	    reposerver.localdomain.
```

**/etc/named.conf**

Add or modify the following options in the /etc/named.conf
```text
options {
	directory 	"/var/named";
	forwarders	{ 8.8.8.8; };
	allow-query     { localhost; 192.168.15.0/24; };

	recursion yes;


zone "localdomain." IN {
	type master;
	file "localdomain.forward";
};

zone "15.168.192.in-addr.arpa" IN {
        type master;
        file "localdomain.rev";
};
```
Add the named service to start with the system and add the nodes to the dns files
Run the service and enjoy your dns..
Feel free to replace localdomain with any valid name for your lab.

Next: [Install Git](create_vm.md)

Back: [Back to homepage](README.md)
