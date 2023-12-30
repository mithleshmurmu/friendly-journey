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
Installing hpl benchmark
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
Now check the file using command
```
ls -all
```
Installing openmpi
```
wget https://download.open-mpi.org/release/open-mpi/v4.1/openmpi-4.1.4.tar.gz
```
Extracting the openmpi tar file
```
tar -xvf openmpi-4.1.4.tar.gz
```
Check the file using command
```
ls -all
```
Now go to openmpi-4.1.4/ directory 
```
cd openmpi-4.1.4/
```
Use ```ls``` command to check the files, we can see the configure files, now execute it.
```
./configure --prefix=/opt/openmpi-4.1.4 --enable-orterun-prefix-by-default
```
If it gives some dependency error. For ex :- C program compiler  files,  then we install it.
```
yum groupinstall "Development Tools"
```
Now again execute configure file.
```
./configure --prefix=/opt/openmpi-4.1.4 --enable-orterun-prefix-by-default
```
Create makefile , HERE we are creating no of threds for our program, 8 means 8 threads we are creating
```
make -j  8
```
Installing make file
```
make install
```
Setting the PATH files
```
echo $PATH
```
```
export PATH=/opt/openmpi-4.1.4/bin/:$PATH
```
To check the files use ```ls /opt/openmpi-4.1.4/``` and ```ls /opt/openmpi-4.1.4/bin/``` commands.
