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
