### Benchmarking using HPL(High Performance Linpack) on centos7 <br>
[ Main File ] <br>
It is used to check the system performance.<br>
This benchmark is designed for following configuration<br.
[ CPU -2<br>
RAM - 8 GB<br>
cores per socket 1 ] <br>
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
To check the files use ```ls /opt/openmpi-4.1.4/``` and ```ls /opt/openmpi-4.1.4/bin/``` ```ls /opt/openmpi-4.1.4/lib/``` commands.
```
echo $LD_LIBRARY_PATH
```
```
export LD_LIBRARY_PATH=/opt/openmpi-4.1.4/lib/:$LD_LIBRARY_PATH
```
Check the files ```ls``` then go to 
```
cd hpl-2.3/setup/
```
Check the files ```ls``` <br>
Now we copy the Make.Linux_PII_CBLAS to hpl directory
```
cp Make.Linux_PII_CBLAS /root/hpl-2.3
```
Now go to hpl directory
```
cd /root/hpl-2.3/
```
[ ```find / -name libsatlas.so.3``` ]<br>
copy the path and paste in the Make.Linux_PII_CBLAS file<br>
Now edit the Make.Linux_PII_CBLAS file
```
vi Make.Linux_PII_CBLAS
```
```
# vim Make.Linux_PII_CBLAS

	>> edit     # ----------------------------------------------------------------------
			# - HPL Directory Structure / HPL library ------------------------------
			# ----------------------------------------------------------------------
			#
				TOPdir       = /root/hpl-2.3

		      # ----------------------------------------------------------------------
			# - Message Passing library (MPI) --------------------------------------
			# ----------------------------------------------------------------------

				MPdir        = /opt/openmpi-4.1.4

				MPlib        = $(MPdir)/lib/libmpi.so	
		    	# ----------------------------------------------------------------------
				# - Compilers / linkers - Optimization flags ---------------------
			# ----------------------------------------------------------------------
			#
				CC           = /usr/bin/gcc

				LINKER       = /usr/bin/gcc
			#
			# ----------------------------------------------------------------------
			# - Linear Algebra library (BLAS or VSIPL) -----------------------------
			# ----------------------------------------------------------------------

				LAlib        = /usr/lib64/atlas/libsatlas.so.3 /usr/lib64/atlas/libsatlas.so.3

	>> <Escape Key> : wq
```
After editing 
```
make arch=Linux_PII_CBLAS
```
If it gives error check for error.<br>
And again run ```make arch=Linux_PII_CBLAS``` command. <br>
Now go to Linux_PII_CBLAS directory.
```
cd /root/hpl-2.3/bin/Linux_PII_CBLAS/
```
Use ```ls``` command to see the files. <br>
Now edit the HPL.dat file.
```
vi HPL.dat
```
```
HPLinpack benchmark input file
Innovative Computing Laboratory, University of Tennessee
HPL.out      output file name (if any)
6            device out (6=stdout,7=stderr,file)
1            # of problems sizes (N)
22848	     Ns
1	     # of NBs
192	     NBs
0            PMAP process mapping (0=Row-,1=Column-major)
1            # of process grids (P x Q)
1            Ps
2            Qs
16.0         threshold
3            # of panel fact
0 1 2        PFACTs (0=left, 1=Crout, 2=Right)
2            # of recursive stopping criterium
2 4          NBMINs (>= 1)
1            # of panels in recursion
2            NDIVs
3            # of recursive panel fact.
0 1 2        RFACTs (0=left, 1=Crout, 2=Right)
1            # of broadcast
0            BCASTs (0=1rg,1=1rM,2=2rg,3=2rM,4=Lng,5=LnM)
1            # of lookahead depth
0            DEPTHs (>=0)
2            SWAP (0=bin-exch,1=long,2=mix)
64           swapping threshold
0            L1 in (0=transposed,1=no-transposed) form
0            U  in (0=transposed,1=no-transposed) form
1            Equilibration (0=no,1=yes)
8            memory alignment in double (> 0)
```
Now we run the benchmark file<br>
```-np 2``` , 2 is the number of processor of the system.<br>
Modify it according to the number of processor in your system.
```
mpirun -allow-run-as-root -np 2 ./xhpl HPL.dat
```




