### xCAT Statefull Installation of Centos 7
Refernces :- https://github.com/openhpc/ohpc/wiki/1.3.X

Install OpenHPC Repository
```
systemctl stop firewalld
```
```
systemctl disable firewalld
```
```
setenforce 0
```
```
hostnamectl set-hostname master.demo.lab
```
Add another network interface host only (ens36)   (use command ```ip a``` to see) . Edit it and provide any static IP address using ```nmtui``` command.
IP address- 192.168.1.1  Netmask - 255.255.255.0
```
vi /etc/hosts
```
#### Add the last line only, first two line would be already exist.
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4<br>
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6<br>
<b>192.168.1.1  master.demo.lab  master</b>
```
yum -y install yum-utils
```
```
wget -P /etc/yum.repos.d https://xcat.org/files/xcat/repos/yum/latest/xcat-core/xcat-core.repo
```
If the above line gives error then use --no-check-certificate
```
wget -P /etc/yum.repos.d https://xcat.org/files/xcat/repos/yum/latest/xcat-core/xcat-core.repo --no-check-certificate
```
```
wget -P /etc/yum.repos.d https://xcat.org/files/xcat/repos/yum/xcat-dep/rh7/x86_64/xcat-dep.repo
```
If the above line gives error then use --no-check-certificate
```
wget -P /etc/yum.repos.d https://xcat.org/files/xcat/repos/yum/xcat-dep/rh7/x86_64/xcat-dep.repo --no-check-certificate
```
We would use host-only interface (ens36) and provide our own IP address (192.168.1.1) and netmask (255.255.255.0) 
```
yum -y install xCAT
```
```
. /etc/profile.d/xcat.sh
```
```
ifconfig ens36 192.168.1.1 netmask 255.255.255.0 up
```
```
chdef -t site dhcpinterfaces="xcatmn|ens36"
```
Copy the centos iso file here. If using windows machine use [scp -r centos-iso-file username@ip-address:/home/username ] or use winscp software to copy the file.
```
copycds /root/CentOS-7-x86_64-DVD-1908.iso
```
```
lsdef -t osimage
```
```
mkdef -t node cn002 groups=compute,all ip=192.168.1.1 mac=mac-address-of-client netboot=xnba arch=x86_64
```
```
chtab key=system passwd.username=root passwd.password=root
```
```
chdef -t site domain=demo.lab
```
```
makehosts
```
```
makenetworks
```
```
makedhcp -n
```
```
makedhcp -a
```
```
makedns -n
```
```
makedns -a
```
```
lsdef -t osimage
```
```
nodeset compute osimage=centos7.7-x86_64-install-compute
```
#### To check status of xcat
```
xcatprobe xcatmn -i ens36
```
If all things are working properly, restart the client machine.
