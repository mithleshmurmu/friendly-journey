### Nagios Installation using openhpc on centos7
By following Documents ...
```
systemctl stop firewalld
```
```
setenforce 0
```
OR Modifying selinux files and restart the system
```
vi /etc/selinux/config
```
```
yum install http://build.openhpc.community/OpenHPC:/1.3/CentOS_7/x86_64/ohpc-release-1.3-1.el7.x86_64.rpm
```
```
yum -y install ohpc-nagios
```
```
yum install tree
```
