# Centos Build Server Scripts
## Description
A set of scripts to configure a CentOs 6.8 virtual machine to build Saife.

## Creating the virtual machine.
Unfortuantly, there are some manual steps to this process. This should be quick though.

1. Create a new virtual machine in virtual-box.
1. Use a vmdk format dynamically allocated disk. That way, it is compatible with vSphere and doesn't take up to much space.
1. Select type Linux, Redhat 64 bit.

## Before starting the VM

Go to settings and set up networking. The script assumes this configuration: Adapter 1 NAT, Adapter 2 Host-only. Adapter 2 will be sending out the mDNS broadcasts from avahi if you choose to install mdns. SSH will only be served on Adapater 2. You will need to make sure you are running a DHCP server on your Host-only network if you are using only ipv4. You can google how to do that in virtualbox. Also, consider increasing the processor count to max recommended value if you want builds to complete faster.

## Installing CentOs
Install CentOs 6.x minimal into the virtual machine.

1. Set the hostname as 'centos-builder'
1.1 Make sure to configure the network(s)! It is easy to miss on the same page as the hostname configuration, you want both of them to "Connect automatically". You should also setup IPv6 to connect automatically. If for some reason centos fails to activate them after you close the configuration, you'll need to double check your virtualbox network settings.
1. Setup timezone as America/Phoenix
1. Setup root password to something you can remember.


If you forget to setup networking, you must login as root and setup your network interfaces. https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/s1-networkscripts-interfaces.html

## Runs scripts
Now you can run the script:
```
bash <(curl https://raw.githubusercontent.com/AnthonyLozano/centos-build-server/master/install)
```

Here is what it does in gory detail. 

1. Creates a user account.
1. Gives it a password.
1. Adds it to the wheel group.
1. Adds the wheel group to the sudoers file.
1. Optionally installs avahi-daemon
1. Sets up iptables firewall
1. Prompts user to upload their ssh key, then turns off password login for sshd.
1. Install some stuff for convenience sake
    1.1 Installs ius-release, which contains high quality upstream stable pacakages for centos. The default repos are mostly garbage but we need them to build saife stuff. This lets us get utilities and modern packages without overriding anthing available in the base repo unless we explicitly ask for it. For example, the git2u-all package lets us get a version of git from this century as opposed to the git package in centos 6 that is many versions behind.
    1.1 Installs fish. Needed for the non-root scripts to run.
    1.1 Installs git2u-all
1 Optionally allows the user to set their default shell to fish.
