Credit: Mithlesh Kumar

Credit: Atip Peethong

### Install this

yum -y install http://build.openhpc.community/OpenHPC:/1.3/CentOS_7/x86_64/ohpc-release-1.3-1.el7.x86_64.rpm

<b>Install OpenHPC Repository</b>
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
##### 192.168.1.1  master.demo.lab  master
```
yum -y install yum-utils
```
```
wget -P /etc/yum.repos.d https://xcat.org/files/xcat/repos/yum/latest/xcat-core/xcat-core.repo
```
If this line gives error then use --no-check-certificate
```
wget -P /etc/yum.repos.d https://xcat.org/files/xcat/repos/yum/latest/xcat-core/xcat-core.repo --no-check-certificate
```
```
wget -P /etc/yum.repos.d https://xcat.org/files/xcat/repos/yum/xcat-dep/rh7/x86_64/xcat-dep.repo
```
If this line gives error then use --no-check-certificate
```
wget -P /etc/yum.repos.d https://xcat.org/files/xcat/repos/yum/xcat-dep/rh7/x86_64/xcat-dep.repo --no-check-certificate
```
We would use host-only interface (ens36) and provide our own IP address (192.168.1.1) and netmask (255.255.255.0) 
```
ifconfig ens36 192.168.1.1 netmask 255.255.255.0 up
```
