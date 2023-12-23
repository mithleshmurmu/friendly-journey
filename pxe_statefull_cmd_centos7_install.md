### PXE Statefull Installation of Centos 7
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

