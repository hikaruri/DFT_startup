# OpenMX setup
[OpenMX](http://openmx-square.org/)のビルドには
- MPICH
- fftw3
- intel MKL(option)
が必要

## MPICH
gccのバージョンに対応したmpichを入れる
```shell script
$ sudo port search mpich
$ sudo port install mpich-gcc8
$ sudo port select --list mpi
mpich-gcc8-fortran
none (active)
$ sudo port select --set mpi mpich-gcc8-fortran
Selecting 'mpich-gcc8-fortran' for 'mpi' succeeded. 'mpich-gcc8-fortran' is now active.
$ mpicc -v
gcc version 8.4.0 (MacPorts gcc8 8.4.0_0) 
```
※OpenMPIでも良いが、MKLと一緒に使えない、
OpenMPI+scalapackを使いたい場合は[こちら](https://qiita.com/hikaruri/items/0fa942c9eacb8930a792)
## fftw3
```shell script
$ sudo port install fftw-3
```
## intel MKL(option)
[intel MKL](https://software.intel.com/content/www/us/en/develop/documentation/get-started-with-mkl-for-macos/top.html)
をダウンロード。ライブラリは/opt/intel/mkl/に入る

## OpenMXのbuild
ソースコードを解凍
```shell script
$ wget http://t-ozaki.issp.u-tokyo.ac.jp/openmx3.8.tar.gz
$ tar -zxvf openmx3.8.tar.gz
```
patchファイルを解凍
```shell script
$ wget http://openmx-square.org/bugfixed/18June12/patch3.8.5.tar.gz
$ cp patch3.8.5.tar.gz openmx3.8/source
$ tar -zxvf patch3.8.5.tar.gz
```
./openmx3.8/source/makefileのLIB、FC、CCをいじる。
### Using MKL
```shell script
FFTROOT = /opt/local
LBSROOT = /opt/intel/mkl

LIB= -L$(FFTROOT)/lib -lfftw3 -L$(LBSROOT)/lib -lmkl_intel_lp64 -lmkl_intel_thread -lmkl_core -lpthread -lgfortran -lmkl_scalapack_lp64 -lmkl_blacs_mpich_lp64 -lmpi_mpifh
CC = mpicc -fopenmp -O3 -I$(LBSROOT)/include -I$(FFTROOT)/include
FC = mpif90 -fopenmp -O3 -I$(LBSROOT)/include
```
### Using normal lapack and blas
```shell script
CC = mpicc -O3 -fopenmp -I/opt/local/include -I/usr/include
FC = mpif90 -O3 -fopenmp -I/usr/include
LIB = -L/opt/local/lib -lfftw3 -llapack -lblas -lgfortran -lmpi_mpifh
```
書き換えたら
```shell script
make all
```
でエラーなく終われば成功。困ったら[スライド](http://www.openmx-square.org/tech_notes/OpenMX-Compile.pdf)をみよ
