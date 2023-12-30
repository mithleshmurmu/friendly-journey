#### Banchmarking using HPL(High Performance Linpack) on centos7
It is used to cheeck the system performance.
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
getenforce
```
It should be permissive
It is used to check the status of cpu.
```
lscpu
```
Now Installation :-
```
yum install epel-release -y
```
```
yum install atlas -y
```
To see the install rpm files
```
rpm -ql atlas
```
```
wget https://netlib.org/benchmark/hpl/hpl-2.3.tar.gz
```
If it gives error use --no-check-certificate
```
wget https://netlib.org/benchmark/hpl/hpl-2.3.tar.gz --no-check-certificate
```
Extract the tar file
```
tar -zxvf hpl-2.3.tar.gz
```


