# VirtualBox configuration

To run "lab_in_a_box" there are a number of things to configure, like networks  
This has to match the host system, or else it just won't work.

## Networks
We add a set of custom natnetworks to connect our VM to, so they can see eachother  
 on the network. If we use the default NAT, this wouldn't work...

Open a cmd.exe and cd into 'C:\Progam Files\Oracle\VirtualBox':
````
VBoxManage natnetwork add --netname natnet1 --network "192.168.15.0/24" --enable
VBoxManage natnetwork add --netname natnet2 --network "10.0.10.0/24" --enable
VBoxManage natnetwork start --netname natnet1
VBoxManage natnetwork start --netname natnet2
````
This will add 2 custom networks to virtualbox without DHCP server, because we want full  
control over the network through our inventory in ansible.

On the "natnet1" network, set some port-forwarding rule, so you can access the given port  
from your windows machine, this make life easier.

First plan for yourself wich VM's need access from port-forwarding and what IP addresses they will get.  
In my setup I reserved the bottom range of 20 addresses for management VM's as Automation Platform,   
Repository Server, GIT server and so on..  
Plan these now and write them down for later, we will make the port-frwards here, but they can be added later.  
The list that needs port-forwarding (in my case):  
| Server     | IP address   | Port      |
| ---------- | ------------ | --------- |
| AAP        | 192.168.15.5 | port 80   |
| PrivateHub | 192.168.15.6 | port 80   |
| GIT        | 192.168.15.7 | port 3000 |
| Satellite  | 192.168.15.8 | port 80   |

Thus the commands to execute to define the port forwarding: 
````
VBoxManage natnetwork modify --netname natnet1 --port-forward-4 "aap:tcp:[]:81:[192.168.15.5]:80" 
VBoxManage natnetwork modify --netname natnet1 --port-forward-4 "hub:tcp:[]:82:[192.168.15.6]:80" 
VBoxManage natnetwork modify --netname natnet1 --port-forward-4 "git:tcp:[]:3000:[192.168.15.7]:3000" 
VBoxManage natnetwork modify --netname natnet1 --port-forward-4 "sat:tcp:[]:83:[192.168.15.8]:80" 
````

Note that the rule is defined followes:  
The rule: "aap:tcp:[]:81:[192.168.15.5]:80"  
Has 6 fields:  
* 'aap'		  The name of the rule, must be unique
* 'tcp' 	  The protocol to use
* '[]'		  The source ip address for the rule, if blank all connection to this port are forwarded on the localhost
* '81'		  The port to forward on the localhost (Windows)
* '[192.168.15.5] The ip addres to forward to (mandatory)
* '80'		  The port on the target machine to connect to


>Check:  
>In the networks page in Virtualbox check the Host-Only Adapter without a number  
>This is the default adapter that vagrant will use for the VM, this has to be on the   
>'192.168.56.1/24' network. If it is... OK (its the default)  

## Resolution
If you have a big screen and want to use the full resolution:

Open a cmd.exe and cd into 'C:\Progam Files\Oracle\VirtualBox':
````
VBoxManage setextradata global GUI/MaxGuestResolution any
````
## Defaults
As we said in the introduction, we would like a separate drive for our setup data,  
this is a little paranoia from my side, if anything happens to my windows setup  
it is easily restored after windows reinstall.

In virtualbox preferences set the 'Default Machine Folder' to the drive letter you use

A small copy can make life easier on virtualbox:  
Copy the 'C:\Program Files\Oracle\VirtualBox\VBoxGuestAdditions.iso' to the root of the drive   
you use for the VM's.


VirtualBox is now ready for your first virtual machine..

Next: [Create your first VM](reposerver.md)

Back: [Back to homepage](README.md)
