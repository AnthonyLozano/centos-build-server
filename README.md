# Centos Build Server Scripts
## Description
A set of scripts to configure a CentOs 6.8 virtual machine to build Saife.

## Creating the virtual machine.
Unfortunately, there are some manual steps to this process. This should be quick though.

1. Create a new virtual machine in virtual-box.
1. Use a vmdk format dynamically allocated disk. That way, it is compatible with vSphere and doesn't take up to much space.
1. Select type Linux, Redhat 64 bit.

## Before starting the VM

Go to settings and set up networking. The script assumes this configuration: Adapter 1 NAT, Adapter 2 Host-only. Adapter 2 will be sending out the mDNS broadcasts from avahi if you choose to install mdns. SSH will only be served on Adapter 2. 

You will need to make sure you are running a DHCP server on your Host-only network if you are using only ipv4. You can google how to do that in VirtualBox. Also, consider increasing the processor count to max recommended value if you want builds to complete faster.

## Installing CentOs
Install CentOs 6.x minimal into the virtual machine.

1. Set the hostname as 'centos-builder'
1.1 Make sure to configure the network(s)! It is easy to miss on the same page as the hostname configuration, you want both of them to "Connect automatically". You should also setup IPv6 to connect automatically. If for some reason CentOs fails to activate them after you close the configuration, you'll need to double check your VirtualBox network settings.
1. Setup timezone as America/Phoenix
1. Setup root password to something you can remember.


If you forget to setup networking, you must login as root and setup your network interfaces. https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/s1-networkscripts-interfaces.html

## Runs scripts
Now you can run the script on the Virtual Machine:
```
bash <(curl https://raw.githubusercontent.com/AnthonyLozano/centos-build-server/master/install)
```

The script will prompt you for a few inputs and pause at times to allow you to copy down useful information. After asking if you want to change the user's default shell to fish you can leave it running unattended.

After running the script you should be able to checkout the SaifeSdkCpp project and build it with maven. 
