### PXE Statefull kicksatrt Installation of Centos 7
#### [Automatic partitioning ] with GUI
Stop and Disable firewall
```
systemctl stop firewalld
```
```
systemctl disable firewalld
```
Disable the selinux file
```
setenforce 0
```
```
hostnamectl set-hostname master.demo.lab
```
Add another network interface host only (ens36)   (use command ```ip a``` to see) . Edit it and provide any static IP address using ```nmtui``` command.
IP address- 172.10.1.1  Netmask - 255.255.255.0 (OR) <br>
ens -> Host only  --> nmtui -->  manual  --> IP 172.10.1.1/24 --> Gateway --> 172.10.1.1
```
vi /etc/hosts
```
#### Add the last line only, first two line would be already exist.
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4<br>
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6<br>
<b>172.10.1.1  master.demo.lab  master</b>
```
systemctl status network
```
```
systemctl restart network
```
#### PXE Boot : Configure PXE Server :-
```
yum -y install syslinux xinetd tftp-server
```
```
mkdir /var/lib/tftpboot/pxelinux.cfg
```
```
cp /usr/share/syslinux/pxelinux.0 /var/lib/tftpboot/
```
```
vi /etc/xinetd.d/tftp
```
```
# line 14: change
disable = no
```
```
systemctl start xinetd
systemctl enable xinetd
```
#### Configure DHCP Server :-
```
yum -y install dhcp
```
```
vi /etc/dhcp/dhcpd.conf
```
```
# create new
# specify domain name
option domain-name     "demo.lab";

# specify name server's hostname or IP address
option domain-name-servers     master.demo.lab;

# default lease time
default-lease-time 600;

# max lease time
max-lease-time 7200;

# this DHCP server to be declared valid
authoritative;

# specify network address and subnet mask
subnet 172.10.1.0 netmask 255.255.255.0 {
    # specify the range of lease IP address
    range dynamic-bootp 172.10.1.200 172.10.1.254;
    # specify broadcast address
    option broadcast-address 172.10.1.255;
    # specify default gateway
    option routers 172.10.1.1;
filename        "pxelinux.0";
next-server     172.10.1.1;
}
```
```
systemctl restart dhcpd
```
#### PXE Boot : Network Install :-
Install OS tp a client computer from PXE Server via network. It's useful if your client computer has no CD/DVD drive.
Download ISO image first to your PXE server. The following example shows the ISO image of CentOS 7 is under /home/iso directory.
<br> The only next command will run on Windows Machine.
```
# scp -r CentOS-7-x86_64-DVD-2009.iso mithlesh@192.168.144.164:/home/mithlesh/
```
```
# cp /home/mithlesh/CentOS-7-x86_64-DVD-2009.iso .
```
```
mkdir -p /var/pxe/centos7
```
```
mkdir /var/lib/tftpboot/centos7
```
```
mount -t iso9660 -o loop /home/iso/CentOS-7-x86_64-DVD-1503-01.iso /var/pxe/centos7
```
<b>(OR)</b> Select the iso file path...
```
mount -t iso9660 -o loop /root/CentOS-7-x86_64-DVD-2009.iso /var/pxe/centos7
```
```
cp /var/pxe/centos7/images/pxeboot/vmlinuz /var/lib/tftpboot/centos7/
```
```
cp /var/pxe/centos7/images/pxeboot/initrd.img /var/lib/tftpboot/centos7/
```
```
cp /usr/share/syslinux/menu.c32 /var/lib/tftpboot/
```
#### Apache httpd : Install httpd :-
```
yum install httpd
```
```
rm -f /etc/httpd/conf.d/welcome.conf
```
```
vi /etc/httpd/conf/httpd.conf
```
```
# line 86: change to admin's email address
ServerAdmin root@localhost

# line 95: change to your server's name
ServerName master.demo.lab:80

# line 151: change
AllowOverride All

# line 164: add file name that it can access only with directory's name
DirectoryIndex index.html index.cgi index.php

# add follows to the end
# server's response header
ServerTokens Prod
```
```
vi /etc/httpd/conf.d/pxeboot.conf
```
```
# create new
Alias /centos7 /var/pxe/centos7
<Directory /var/pxe/centos7>
    Options Indexes FollowSymLinks
    # IP address you allow to access
    Require ip 127.0.0.1 172.10.1.0/24
</Directory>
```
```
systemctl restart httpd
```
#### Kickstart Installation :-
```
python -c 'import crypt,getpass; \
print(crypt.crypt(getpass.getpass(), \
crypt.mksalt(crypt.METHOD_SHA512)))'
```
password:root<br>
$6$5IeCoTBSmhk17J4p$HBzQX8pMd7kK/f963mfPyzl/KhTDyneU6IEKvbQSNPJr2XSueE3S9VaKauGnoNixb4KncGVs3K1qPdJEYollC0
```
mkdir /var/www/html/ks
```
[Automatic partitioning ]
```
vi /var/www/html/ks/centos7-ks.cfg
```
```
# create new

install

# automatically proceed for each steps
autostep

# reboot after installing
reboot

# encrypt algorithm
auth --enableshadow --passalgo=sha512

# installation source
url --url=http://172.10.1.1/centos7/

# install disk
ignoredisk --only-use=sda

# keyboard layouts
keyboard --vckeymap=in-eng --xlayouts='in (eng)'

# system locale
lang en_US.UTF-8

# network settings
network --bootproto=dhcp --ipv6=auto --activate --hostname=localhost

# root password you generated above
rootpw --iscrypted $6$5IeCoTBSmhk17J4p$HBzQX8pMd7kK/f963mfPyzl/KhTDyneU6IEKvbQSNPJr2XSueE3S9VaKauGnoNixb4KncGVs3K1qPdJEYollC0

# timezone
timezone Asia/Kolkata --isUtc --nontp

# bootloader's settings
bootloader --location=mbr --boot-drive=sda

# initialize all partition tables
zerombr
clearpart --all --initlabel

# partitioning
autopart --type=plan --fstype=ext4

%packages
@^gnome-desktop
@core
%end
```
```
chmod 644 /var/www/html/ks/centos7-ks.cfg
```
```
vi /var/lib/tftpboot/pxelinux.cfg/default
```
```
# create new
timeout 100
default menu.c32

menu title ########## PXE Boot Menu ##########
label 1
   menu label ^1) Install CentOS 7
   kernel centos7/vmlinuz
   append initrd=centos7/initrd.img ks=http://172.10.1.1/ks/centos7-ks.cfg

label 2
   menu label ^2) Boot from local drive
   localboot
```
```
systemctl restart httpd
systemctl restart dhcpd
systemctl restart xinetd
```
