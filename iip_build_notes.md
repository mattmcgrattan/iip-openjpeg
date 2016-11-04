## Install dependencies:


Instructions assume vagrant box and working in:
**~/**
replace:
**~/** 
and 
**/home/vagrant** 
throughout, with the path you are working under.


```
sudo yum update

sudo yum install epel-release

sudo yum install scl-utils scl-utils-build

sudo yum install memcached libjpeg-devel libtiff-devel centos-release-scl git cmake zlib-devel libpng-devel lcms2-devel wget

sudo yum devtoolset-4
```

## Invoke devtoolset-4 to get up to date versions of compilers, etc.

```
scl enable devtoolset-4 bash
```
## Clone and build OpenJPEG

```
git clone https://github.com/uclouvain/openjpeg.git
cd openjpeg
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr .. && make
sudo make install
sudo ldconfig
```

## hacks to make IIP compile 
*IIP seems to look in the wrong place for the lib files, and the .so*

*Check for the location of the lib files, should be /usr/include/ and /usr/lib/ as below, but if not, check /usr/local/include and /usr/local/lib/*

```
mkdir /home/vagrant/openjpeg/bin

cp /usr/include/openjpeg-2.2/*.h /home/vagrant/openjpeg/src/lib/openjp2/

cp /usr/lib/libopenjp2.so /home/vagrant/openjpeg/bin/
```

## Test 

```
opj_compress -h
```
## Clone and compile IIP

```
cd ~/


git clone https://github.com/ruven/iipsrv.git

cd ~/iipsrv

./autogen.sh

./configure --with-openjpeg=/home/vagrant/openjpeg/

make
```

## Check everything linked.
```
cd src
ldd iipsrv.fcgi

	linux-vdso.so.1 =>  (0x00007ffe7fd08000)
	libopenjp2.so.7 => /lib/libopenjp2.so.7 (0x00007fb29e99f000)
	libnsl.so.1 => /lib64/libnsl.so.1 (0x00007fb29e785000)
	libz.so.1 => /lib64/libz.so.1 (0x00007fb29e56f000)
	libpthread.so.0 => /lib64/libpthread.so.0 (0x00007fb29e353000)
	libjpeg.so.62 => /lib64/libjpeg.so.62 (0x00007fb29e0fd000)
	libtiff.so.5 => /lib64/libtiff.so.5 (0x00007fb29de89000)
	libstdc++.so.6 => /lib64/libstdc++.so.6 (0x00007fb29db81000)
	libm.so.6 => /lib64/libm.so.6 (0x00007fb29d87e000)
	libgomp.so.1 => /lib64/libgomp.so.1 (0x00007fb29d667000)
	libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x00007fb29d451000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fb29d08e000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fb29ebf0000)
	libjbig.so.2.0 => /lib64/libjbig.so.2.0 (0x00007fb29ce82000)
```