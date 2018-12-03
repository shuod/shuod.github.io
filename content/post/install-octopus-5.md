+++

date = "2017-01-14"

title = "Installing octopus 6.0 (Serial) in Centos 6.8 "

tags = ["VPS", "Linux","Science"]

Categories = ["Science", "Linux"]

+++

Octopus is a scientific simulation software for solving quantum problem. (http://www.tddft.org/programs/octopus/wiki/index.php/Main_Page)

Recently, there is a new version 6.0 released (2016-09-05), which has a major improvement. Here we try to install it on the Centos 6.8 (with minimal installation)

## First, install Centos 6.8 with minimal installation choice.

## Installation of octopus 6

mainly follow their manual here : http://www.tddft.org/programs/octopus/wiki/index.php/Manual:Building_from_scratch 

### 1. install several compilers to use.

You can check your current available compilers by 

```bash
# search for all available compilers
yum search all compiler
```

then install gcc for C and gfortran for FORTRAN and perl etc by

```bash
# install some compilers
yum -y install gcc gcc-c++ make libXext-devel perl-Time-HiRes
yum -y groupinstall "Development Tools"
yum -y install libX11-devel mesa-libGL-devel perl-Time-HiRes
yum -y install gcc-gfortran
```

There is some note for the compilers:  (http://www.tddft.org/programs/octopus/wiki/index.php/Manual:Appendix:Compilers#Linux)

* gfortran: 4.1.1 or newer, For the parallel version you need at least 4.3; use the same version for compling the blas, lapack and fftw3 (this may cause problem if uses different version with octopus). 
  Recommended flags:
  FCFLAGS="-march=athlon64 -msse2 -mfpmath=sse -malign-double -funroll-loops -O3"
* g95 : 4.0.3 (g95 0.91) May 24 2007 or newer. Because it doesn't recognize the linemarkers by preprocessor, we need to use -P flag pass it to CPP. 
  Flag:
  FC=g95
  FCFLAGS="-O3 -funroll-loops -ffast-math"
  FCCPP="cpp -ansi-P"


And setup the target folders:

```bash
mkdir -p /opt/octopus6/lib

```



### 2. Install BLAS 

Download it from [http://www.netlib.org/blas/blas.tgz](http://www.netlib.org/blas/blas.tgz) and extract it:

```bash
tar -zvxf blas.tgz
cd Blas-3.5.0
```

Change the makefile "make.inc" to add:

```bash
FORTRAN  = gfortran
OPTS     = -msse2 -maligh-double -mfpmath=sse -funroll-loops -O3
DRVOPTS  = $(OPTS)
NOOPT    =
LOADER   = gfortran
LOADOPTS =
```

then 

```bash
make
cp blas_LINUX.a /opt/octopus6/lib/libblas.a
```



### 3. Install LAPACK

Download it from [http://www.netlib.org/lapack/lapack.tgz](http://www.netlib.org/lapack/lapack.tgz) and extract it

``` 
cp make.inc.example make.inc
```

Change "make.inc" to

```bash
FORTRAN  = gfortran
OPTS     = -msse2 -maligh-double -mfpmath=sse -funroll-loops -O2
DRVOPTS  = $(OPTS)
NOOPT    =
LOADER   = gfortran
LOADOPTS =
```

In the makefile example from LAPACK, they gave -O2 which is different from octopus's manual. I go with "-O2"

```bash
make lib
cp liblapack.a /opt/octopus6/lib/liblapack.a
```

### 3 Installation of GSL

Download GSL (1.16) from  [ftp://ftp.gnu.org/gnu/gsl/](ftp://ftp.gnu.org/gnu/gsl/)

Extract and configure it as:

```bash
./configure --prefix=/opt/gsl CC="gcc" CFLAGS="-m64" --disable-shared --enable-static
```

```bash
make
make install
```

Please note the version. It is not clear whether 2.x is supported. But it's safe to use 1.x. Also, if gsl is not properly installed, there might be error in in compiling libxc3 later. So if you got error in libxc3, you could go back to check the gsl.



### 4 Installation of  FFTW3

Download FFTW3 (3.3.4) from  http://www.fftw.org/

Extract and configure it as:

```bash
./configure  --prefix=/usr CC="gcc" CFLAGS="-m64" F77="gfortran" F77FLAGS="-march=athlon64 -msse2 -mfpmath=sse -malign-double -funroll-loops -O3"
make
make install
```



### 5 Installation of libxc 3.0

One of the features of octopus 6, is it will use libxc, which has more functionals choices available.

Download libxc 3.0 from http://www.tddft.org/programs/octopus/down.php?file=libxc/libxc-3.0.0.tar.gz 

```
./configure --prefix=/opt/libxc3 CC="gcc" CFLAGS="-m64" FC="gfortran" FCFLAGS="-march=athlon64 -msse2 -mfpmath=sse -malign-double -funroll-loops -O3"
make
make install
```



### 6 Installation of octopus 6

After properly installed those package, we then define several environment variable before proceed to octopus.

```bash
export LIBS_BLAS=/opt/octopus6/lib/libblas.a
export LIBS_LAPACK=/opt/octopus6/lib/liblapack.a
export LIBS_FFT=/usr/lib/libfftw3.a
```

```bash
./configure CC="gcc" CFLAGS="-m64" FC="gfortran" FCFLAGS="-msse2 -mfpmath=sse -malign-double -funroll-loops -O3" --prefix=/opt/octopus6 --with-gsl-prefix=/opt/gsl --with-libxc-prefix=/opt/libxc3

make
make install
```



There are also other switches for ./configure for octopus 6 and I just list them here (from [1]):

```bash
# ./configure 
--prefix=/usr/local/octopus-6.0.0  \
--with-libxc-prefix=/usr/local/libxc-2.1.2 \
--with-libxc-include=/usr/local/libxc-2.1.2/include \
--with-gsl-prefix=/usr/local/gsl-1.16 \
--with-blas=/usr/lib64/libblas.a \ 
--with-arpack=/usr/lib64/libarpack.so.2 \ 
--with-fft-lib="-L/usr/local/fftw-3.3.3-single/lib" \
--disable-zdotc-test \
--enable-single \
--enable-mpi
```



#### Reference:

1. Basic Configuration of Octopus 5.0.0 with OpenMPI on CentOS 6 

   (https://linuxcluster.wordpress.com/2015/12/15/basic-configuration-of-octopus-5-0-0-with-openmpi-on-centos-6/)

2. Official manual for general install.

   [http://www.tddft.org/programs/octopus/wiki/index.php/Manual:Building_from_scratch](http://www.tddft.org/programs/octopus/wiki/index.php/Manual:Building_from_scratch)

