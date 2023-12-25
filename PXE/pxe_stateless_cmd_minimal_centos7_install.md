### PXE Stateless Installation of Centos 7
References :--
<br>1.https://www.server-world.info/en/note?os=CentOS_7&p=pxe
<br>2.https://www.server-world.info/en/note?os=CentOS_7&p=pxe&f=4
#### [ Minimal Installation ]
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
ens33 -> NAT<br>
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
#### PXE Boot : Diskless Client :--
```
yum -y install dracut-network nfs-utils
```
```
mkdir -p /var/lib/tftpboot/centos7/root
```
```
yum groups -y install "Minimal Install" --releasever=7 --installroot=/var/lib/tftpboot/centos7/root/
```
```
python -c 'import crypt,getpass; \
print(crypt.crypt(getpass.getpass(), \
crypt.mksalt(crypt.METHOD_SHA512)))'
```
Password:root
$6$d6Gj70RPZpc1JWiF$qCt3fqcE7HB9kXyNONs7csxsUXZfCwrQp5bTmkB5vGyqtWyDF7/TL7OgR3DVTVGMPnCT1lhdGhkkrrIOARv4q/<br>
```
vi /var/lib/tftpboot/centos7/root/etc/shadow
```
Set root password generated above
root:$6$d6Gj70RPZpc1JWiF$qCt3fqcE7HB9kXyNONs7csxsUXZfCwrQp5bTmkB5vGyqtWyDF7/TL7OgR3DVTVGMPnCT1lhdGhkkrrIOARv4q/:18353:0:99999:7:::
```
vi /var/lib/tftpboot/centos7/root/etc/fstab
```
```
none    /tmp        tmpfs   defaults   0 0
tmpfs   /dev/shm    tmpfs   defaults   0 0
sysfs   /sys        sysfs   defaults   0 0
proc    /proc       proc    defaults   0 0
```
```
wget -P /var/lib/tftpboot/centos7/ \
http://mirror.centos.org/centos/7/os/x86_64/images/pxeboot/vmlinuz \
http://mirror.centos.org/centos/7/os/x86_64/images/pxeboot/initrd.img
```
```
vi /var/lib/tftpboot/pxelinux.cfg/default
```
```
# create new
default centos7

label centos7
    kernel centos7/vmlinuz
    append initrd=centos7/initrd.img root=nfs:172.10.1.1:/var/lib/tftpboot/centos7/root rw selinux=0
```
#### Configure NFS server :--
```
vi /etc/exports
```
```
/var/lib/tftpboot/centos7/root 172.10.1.0/24(rw,no_root_squash)
```
```
systemctl start rpcbind nfs-server
systemctl enable rpcbind nfs-server
```
```
systemctl start xinetd
systemctl restart dhcpd
```
