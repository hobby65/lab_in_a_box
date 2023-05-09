# Inventory creation

Nothing works in AWX or Ansible Automation Platform without a decent inventory.  
We could create a dynamic inventory, but we keep it stupidly simple for our  
lab environment.  
We create this inventory in a folder named 'inventory' and push this as inventory  
into a new repository in our git server. After this the inventory can be configured in   
Ansible Automation Platform as inventory. And magically your controller knows where to do  
things :-) 

Here is a tree view of the inventory:
````
.
├── group_vars
│   ├── all
│   │   ├── env.yml
│   │   └── satellite.yml
│   ├── dev.yml
│   ├── vbox8.yml
│   └── vbox9.yml
├── hosts.ini
├── host_vars
│   ├── aapserver.localdomain.yml
│   ├── gitserver.localdomain.yml
│   ├── privatehub.localdomain.yml
│   ├── reposerver.localdomain.yml
│   ├── satellite.localdomain.yml
│   ├── testhost1.localdomain.yml
│   ├── testhost2.localdomain.yml
│   ├── testhost3.localdomain.yml
│   └── virtserver.localdomain.yml
└── vars.yml
````
We will walk you through every file in the inventory to see whats in there and why.  

## group_vars
This is the directory where all the groups are hosted and the vars for these groups are defined.  
We don't want to repeat the definition of vars, so we stack them in layers, using the groups and children.  
### group_vars/all
The 'all' folder, holds variables that are added to 'ALL' hosts, no matter what the filename all  
hosts know these vars.  

In this part of the inventory you typically set variables that apply to 'ALL' machines in the environment  
You can split this up into multiple files, so you can give them meaningfull names. This is a simplified  
version of the actual inventory I use, so splitting up in several files is a good thing to do.  
* env.yml  
This file holds vars that are generic for the environment.   
````
env: Development
envShort: D
etc_hosts: all
etc_hosts_enabled: true
motd_message: Ongeautoriseerd gebruik van dit computersysteem en de daaraan gekoppelde
  apparatuur en programmatuur is verboden
ntp_timezone: Europe/Amsterdam
````

* satellite.yml
This file defines if there is a satellite (in my case its True), but here is false  
so I can use the artifact server as local repository server in my ansible code.  
````
artifact_server: reposerver.localdomain
middleware_server: 'http://{{ artifact_server }}/pub/middleware'
satellite_present: false
satellite_organization_label: Default_Organization
satellite_organization_name: TEST
satellite_server: satellite.localdomain
````
### group_vars/dev.yml
As you can see the closer we get to the actual system, the more detail there will be in   
the defined vars, here we define the vars for the dev environment, for acceptance or prod,  
they would be different  

````
dns_domain: localdomain
dns_servers:
- 8.8.8.8
location_name: DEV
ntp_servers:
- ntp1.time.nl
- ntp2.time.nl
redhat_insights_enabled: false
````

### group_vars/vbox8.yml
Here we start to link the deployment environment to the actual machines, the networks are defined  
as they are defined in the virtualbox configuration. The host for the virtualbox is also defined here.  
The last 3 vars in this file, are defined to tell the deployment playbook, what template 'box' to use  
and in what virtualboxgroup the vm is to be built.  
````
vbox:
  server: virtserver.localdomain
  networks:
    backend1:
      netmask: 255.255.255.0
      network: 10.0.10.0
      net_name: natnet2
    frontend1:
      gateway: 192.168.15.1
      netmask: 255.255.255.0
      network: 192.168.15.0
      net_name: natnet1
  vm_folder: 'F:'
  vm_group: datacenter
  template_box: redhat/rhel-8
````

### group_vars/vbox9.yml
Same as the vbox8.yml, with the exception of the template name(1).
````
vbox:
  server: virtserver.localdomain
  networks:
    backend1:
      netmask: 255.255.255.0
      network: 10.0.10.0
      net_name: NatNetwork
    frontend1:
      gateway: 192.168.15.1
      netmask: 255.255.255.0
      network: 192.168.15.0
      net_name: natnet1
  vm_folder: 'F:'
  vm_group: datacenter
  template_box: redhat/rhel-9
````

## host_vars/virtserver.localdomain.yml
Here the deployment host is defined together with the correct parameters for the
deployment playbooks(this makes it work).
````
hostname: virtserver.localdomain
ansible_user: <your windows username here>
ansible_connection: ssh
ansible_shell_type: powershell
primary_interface:
  ip: 192.168.56.1
  network: frontend1
````

## host_vars/testhost1.localdomain.yml
A host to deploy in the developement environment, this is its definition....
````
architecture: 64bit
hostname: testhost1.localdomain
os:
  name: rhel
  version: 1.0.0
primary_interface:
  ip: 192.168.15.100
  network: frontend1
root_disk_size: 50
size: s
type: vm
````

## hosts.ini
In hosts.ini, we connect the dots....  

As you can see a host has a group, which has a group...  
This builds layers of vars that are stacked to form a host definition that  
can be deployed in an automated environment.  
````
[dev]

[dev:children]
vbox8
test_env

[vbox8]
reposerver.localdomain

[vbox8:children]
deploy

[deploy]
testhost1.localdomain
testhost2.localdomain
testhost3.localdomain

[test_env]
aapserver.localdomain
gitserver.localdomain
privatehub.localdomain
virtserver.localdomain
````
Just be sure that a host must be a member of at least one of the vbox groups,  
or else the deployment will fail, using the infrastructure plays.   

*(1) as you can see, I deviated from my own statement to 'NOT' to repeat variable definition*  
  *The reason here is readability ( or ignorance ), but this way it is clearer what has changed.*  
  *It would be nice to add another layer of grouping here..*  

Next: [Create 'box' templates](create_box_template.md)

Back: [Back to homepage](README.md)
