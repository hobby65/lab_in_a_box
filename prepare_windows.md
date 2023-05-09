# Introduction

In order to run This setup under Windows the following preparation steps  
are needed on a Windows. All steps for preparation can be found on the  
internet and the links to these sources can be found in the links.md file  
in this repository. 

## Disable Hyper-V virtualization

In Windows 11 Home edition, there is nothing to do here, on Windows 11 Pro the  
feature Hyper-V must be disabled.

To disable Hyper-V use the following steps:

Open a powershell with **administrator** privileges:

Run the following powershell command:
````
Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-All
````
Windows will now uninstall these features.
Reboot when done
 
## Disable DeviceGuard code integrity

In order to use VirtualBox without performance issues, code integrity (within Defender)  
must be disabled. To disable this, open the register editor  
The registry key to be adapted is the following key:
````
HKLM\SYSTEM\CurrentControlSet\Control\DeviceGuard\HyperVisorEnforcedCodeIntegrity\Enabled
````
needs to get the value “0”, his disables the function after reboot.


## Install OpenSSH

OpenSSH server is builtin for modern windows releases, its just not installed by default.  
Why we use OpenSSH instead of WinRM (Windows Remote Management) for ansible and vagrant:  
If you enable WinRM on your system, you do this for all networks, so with the WinRM enabled  
you might be open for attacks. You might say that OpenSSh holds the same problem, dut we can  
limit OpenSSH to listen on a specific (internal) interface with no connection option from  
the bad outside world, thats what we do here.  
To install it om your machine, follow the steps:
````
Open 'Settings'
Click on 'Apps'
In the right pane, select 'Optional features'
Click on 'View features'
In the search box, type 'OpenSSH'
check the 'OpenSSH server' checkbox
click 'Install'
````
````
The feature will be installed now...
To start the OpenSSh server service:
Open the 'services' App
Look for 'OpenSSH server', right-click and select 'start'
Change the 'Startup Type' for this service to 'autostart'
````
The most secure setup is to let the OpenSSH server listen to the host-only network address.  
eg. 192.168.56.1 this can be changed in the file: C:\ProgramData\ssh\sshd_config  
Open the file from a powershell with **administrator** privileges with notepad, so you can edit the file and save it.

Set the default shell for OpenSSH connections to 'Powershell' for ansible..

Open a powershell with administrator privileges:

Run the following powershell command:
````
New-ItemProperty -Path "HKLM:\SOFTWARE\OpenSSH" -Name DefaultShell -Value "C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" -PropertyType String -Force
````

Windows is now ready to receive your ANSIBLE commands through SSH from a vm in vbox.

Next: [Configuring Virtualbox](virtualbox_config.md)

Back: [Back to homepage](README.md)
