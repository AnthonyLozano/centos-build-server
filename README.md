Create a new virtual machine in virtual-box
Use a vmdk format dynamically allocated disk. That way, it is compatible with vSphere and doesn't take up to much space.
Select type Linux, Redhat 64 bit.

*Before starting the VM*

Go to settings and set up networking however you'd like. For personal use, I recommend one NAT so you can get Internet access and one host-only so you can access the machine over SSH internally. The personal use script assumes this configuration: Adapter 1 NAT, Adapter 2 Host-only. Adapter 2 will be sending out the mdns broadcasts from avahi if you choose to install mdns. You will need to make sure you are running a DHCP server on your Host-only network if you are using only ipv4. You can google how to do that in virtualbox. Also, consider upping the processor count to max recommended value if you want builds to complete faster.

Install CentOs 6 minimal into the virtual machine.

1. Set the hostname as 'centos-builder'
1.1 Make sure to configure the network(s)! It is easy to miss on the same page as the hostname configuration, you want both of them to "Connect automatically". If you want to be extra fancy, setup IPv6 to connect automatically as well! If for some reason centos fails to activate them after you close the configuration, you'll need to double check your virtualbox network settings.
1. Setup timezone as America/Phoenix
1. Setup root password as fishstick1


If you forget, you must login as root and setup your network interfaces. https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/s1-networkscripts-interfaces.html

Now you can run the script:


Here is what it does in gory detail. 

Creates a user account. Gives it a password. Adds it to the wheel group. Adds the wheel group to the sudoers file.
useradd user
passwd user
usermod -aG wheel user



#install some stuff for convenience sake
#We want ius-release, which contains high quality upstream stable pacakages for centos. The default repos are mostly garbage but we need them to build saife stuff. This lets us get utilities and modern packages without overriding anthing available in the base repo unless we explicitly ask for it. For example, the git2u package lets us get a version of git from this century as opposed to the git package in centos 6 that is many versions behind. 

yum install -y epel-release
yum install -y https://rhel6.iuscommunity.org/ius-release.rpm

Alright now we can get some goodies.



# This is optional. Gets you a shell for the 90s, fish. Installs some packages for it too. 
wget -P /etc/yum.repos.d/ http://download.opensuse.org/repositories/shells:fish:release:2/CentOS_6/shells:fish:release:2.repo
yum -y install fish
chsh /usr/bin/fish user

# If you want mdns for local domain names:
yum install -y avahi

# bullshit firewall rules cause centos and or its packages are such enterprisy goodness:

sshd_line_num=$(sudo iptables -nL --line-numbers -v | grep dpt:22 | head -c 1)
iptables -I INPUT $sshd_line_num -p udp -m state -m udp --dport 5353 --state NEW -j ACCEPT -m comment --comment "allow avahi rule"
iptables-save > /etc/sysconfig/iptables
iptables-restore < /etc/sysconfig/iptables






Post-guide notes: Change password and update it.