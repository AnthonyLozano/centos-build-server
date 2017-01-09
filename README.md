# Centos Build Server Scripts
## Description
A set of scripts to configure a CentOs 6.8 virtual machine to build Saife.

## Creating the virtual machine.
Unfortunately, there are some manual steps to this process. This should be quick though.

1. Create a new virtual machine in virtual-box.
1. Use a vmdk format dynamically allocated disk. That way, it is compatible with vSphere and doesn't take up to much space.
1. Select type Linux, Redhat 64 bit.

## Before starting the VM

Go to settings and set up networking. The script assumes this configuration: Adapter 1 NAT, Adapter 2 Host-only. You can add a new Host-only network to VirtualBox from the VirtualBox preferences menu (Not the virtual machine's settings menu). Adapter 2 will be sending out the mDNS broadcasts from avahi if you choose to install mdns. SSH will only be served on Adapter 2. 

If you are using only ipv4, You will need to make sure you are running a DHCP server on your Host-only network. You can google how to do that in VirtualBox. If you are using ipv6 and mdns, you don't need to worry about the DHCP server. Also, consider increasing the processor count to max recommended value if you want builds to complete faster.

## Installing CentOs
Install CentOs 6.8 minimal into the virtual machine. You can get the ISO from http://isoredirect.centos.org/centos/6/isos/x86_64/

1. Set the hostname as 'centos-builder'
1.1 Make sure to configure the network(s)! It is easy to miss on the same page as the hostname configuration, you want both of them to "Connect automatically". You should also set IPv6 method to Automated on eth1. If for some reason CentOs fails to activate them after you close the configuration, you'll need to double check your VirtualBox network settings.
1. Setup timezone as America/Phoenix
1. Setup root password to something you can remember.


If you forget to setup networking, you must login as root and setup your network interfaces. https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/s1-networkscripts-interfaces.html

## Run scripts
Now you can run the script on the Virtual Machine. Login as root and run:
```
bash <(curl https://raw.githubusercontent.com/AnthonyLozano/centos-build-server/master/install)
```

The script is going to create a new user account named 'user' for you to use. It will also disable password login and root login over ssh, so it is important to install your ssh key for the 'user' account when prompted.

Additionally, the script will prompt you for a few inputs and pause at times to allow you to copy down useful information. After asking if you want to change the user's default shell to fish you can leave it running unattended.

After running the script you should be able to checkout the SaifeSdkCpp project and build it with maven. 
