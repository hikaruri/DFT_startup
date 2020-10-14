# DFT_startup
研究室用メモ。Macの環境構築。

- 最低限の設定
- [OpenMX](http://openmx-square.org/)
- [CrySPY](https://github.com/Tomoki-YAMASHITA/CrySPY)
- [soiap](https://github.com/nbsato/soiap)
- [Quantum Espresso](https://www.quantum-espresso.org/)

[kaityo256/lab_startup](https://github.com/kaityo256/lab_startup)も参考にせよ

# 最低限の設定
- パッケージ管理ソフト(macports)
- C/C++/fortranコンパイラ
- gnuplot
- Python
## 下準備
Xcodeと[Command line tools](https://developer.apple.com/download/more/?=command%20line%20tools)を入れる。
Terminalを立ち上げて
```shell script
$gcc -v
(中略)
Apple LLVM version 10.0.0 (clang-1000.11.45.5)
```
と出てきたら成功(clangと出てくれば大丈夫)。
## パッケージ管理ソフト
[macports](https://github.com/macports/macports-base/releases/tag/v2.6.3)
環境は全て/opt/に入るので、最悪ここを消せば元に戻る(らしい)。
```shell script
$ sudo port selfupdate
```
を叩いておく
## C/C++/fortranコンパイラ
最新のgccを検索
```shell script
$ sudo port search gcc
```
gcc8を入れる
```shell script
$ sudo port install gcc8
```
インストールしたらselect
```shell script
$ sudo port select --list gcc
  mp-gcc8
  none (active)
$ sudo port select --set gcc mp-gcc8
Selecting 'mp-gcc8' for 'gcc' succeeded. 'mp-gcc8' is now active.
$ sudo port select --list gcc
  mp-gcc8 (active)
  none
```
一旦、terminalをRebootしてgccにgcc8が通っているのか確認
```shell script
$gcc -v
(中略)
gcc version 8.4.0 (MacPorts gcc8 8.4.0_0) 
```
と出たら成功。
```shell script
$gcc -v
(中略)
Apple LLVM version 10.0.0 (clang-1000.11.45.5)
```
は失敗
## gnuplot
For Catalina, macports version's gnuplot has some bugs. 
Please install [the binary version](https://csml-wiki.northwestern.edu/index.php/Binary_versions_of_Gnuplot_for_OS_X)
macports経由で
```shell script
$ sudo port install gnuplot
```
```shell script
$ gnuplot
(中略)
Terminal type is now 'aqua'
gnuplot> 
```
と出れば成功。
※homebrewを使うと色々オプション設定が必要

## Python
port経由でpython3とpipを入れる
```shell script
$ sudo port install python37
$ sudo port install py37-pip
$ python3 --version
Python 3.7.5
```
Virtualenvで仮想環境を用意する準備
```shell script
$ pip install virtualenv
$ virtualenv --version
virtualenv 20.0.28
```
仮想環境env1を作る
```shell script
virtualenv env1
```
env1をactivate
```shell script
source env1/bin/activate
```
activateしてからpip installをすることによってソースコード毎に違う環境で作業出来る。
CrySPY専用の環境もこれで作る。
※最初からanacondaを使って逃げてもいい。pipenv、poetryなどでも良い。
# OpenMX
[OpenMX](http://openmx-square.org/)のビルドには
- MPICH
- intel MKL
- fftw3
が必要
## MPICH
gccのバージョンに対応したmpichを入れる
```shell script
$ sudo port search mpich
$ sudo port install mpich-gcc8
```
入ったらバージョンを確認
```shell script
$ mpicc -v
gcc version 8.4.0 (MacPorts gcc8 8.4.0_0) 
```
※OpenMPIはMKLと一緒に使えない、OpenMPI+scalapackを使いたい場合は[こちら](https://qiita.com/hikaruri/items/0fa942c9eacb8930a792)
## intel MKL
[intel MKL](https://software.intel.com/content/www/us/en/develop/documentation/get-started-with-mkl-for-macos/top.html)
をダウンロード。ライブラリは/opt/intel/mkl/に入る
## fftw3
```shell script
$ sudo port install fftw3
```
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
./openmx3.8/source/makefileのLIB、FC、CCをいじる
```shell script
FFTROOT = /opt/local
LBSROOT = /opt/intel/mkl

LIB= -L$(FFTROOT)/lib -lfftw3 -L$(LBSROOT)/lib -lmkl_intel_lp64 -lmkl_intel_thread -lmkl_core -lpthread -lgfortran -lmkl_scalapack_lp64 -lmkl_blacs_mpich_lp64 -lmpi_mpifh
CC = mpicc -fopenmp -O3 -I$(LBSROOT)/include -I$(FFTROOT)/include
FC = mpif90 -fopenmp -O3 -I$(LBSROOT)/include
```
書き換えたら
```shell script
make all
```
でエラーなく終われば成功。困ったら[スライド](http://www.openmx-square.org/tech_notes/OpenMX-Compile.pdf)をみよ
## CrySPYのsetup
- find_wy, 
