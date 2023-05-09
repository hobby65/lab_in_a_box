# How to create a template (box) VM

To have a VM deployable with vagrant, you need something like a 'box' file.  
This is nothing more then a template machine in which some standard options are  
set, so vagrant/virtualbox can configure the machine for you.  

There is a pretty good description out there (see links page), on how to create   
such a 'box' file. Nothing to add to the description, but we might add some   
other things that might be handy to us, like a ansible user with the appropriate  
key, so we can run playbooks against it, when it is deployed.  

So the description can be found here:  
[link](https://oracle-base.com/articles/vm/create-a-vagrant-base-box-virtualbox)

Follow the description until just before shutting down and packaging the box.  
Then you add the 'ansible' user and group, the ansible key to authorized_keys...  

then shutdown the VM and package it as the manual suggests..  
the name is not important for now, but it should be in your vbox(x).yml file  
as the template name...(see inventory)  

Next: [Install Satellite](install_satellite.md)

Back: [Back to homepage](README.md)
