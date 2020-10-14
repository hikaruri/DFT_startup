# DFT_startup
研究室用メモ。Macの環境構築。

- 最低限の設定
- [OpenMX](http://openmx-square.org/)
- [CrySPY](https://github.com/Tomoki-YAMASHITA/CrySPY)
- [soiap](https://github.com/nbsato/soiap)
- [Quantum Espresso](https://www.quantum-espresso.org/)

[kaityo256/lab_startup](https://github.com/kaityo256/lab_startup)も参考にせよ

# 最低限の設定
- macports
- C/C++/fortran compiler
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

## macports
[macports](https://github.com/macports/macports-base/releases/tag/v2.6.3)
環境は全て/opt/に入るので、最悪ここを消せば元に戻る(らしい)。
```shell script
$ sudo port selfupdate
```
を使う前に叩いておく。
※Homebrewを使っても良い

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
とclangのままだと失敗

## gnuplot
### For Catalina User
For Catalina, macports version's gnuplot has [some bugs](https://qiita.com/_shin_/items/9d1482b7f21d7f2bc8c6).
Please install [the binary version](https://csml-wiki.northwestern.edu/index.php/Binary_versions_of_Gnuplot_for_OS_X).
### Others
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

## Python
### anacondaを使う場合(Easy)
anacondaのHPから[ダウンロード](https://www.anaconda.com/products/individual)
すぐにterminalでpythonが使えるように.zshrcが書き換えられてるはず。
```shell script
conda create --name env1
conda activate env1
```
でbase環境以外の仮想環境を作ることができる。CrySPY用の環境は全てココに入れたほうが良い(baseに入れてもCrySPYしか使わないのであればなんとかなる)

### virtualenvを使う場合
port経由でpython3とpipを入れる
```shell script
$ sudo port install python37
$ sudo port install py37-pip
$ sudo port select --list python
  none
  python27-apple (active)
  python37
$ sudo port select --set python python37
Selecting 'python37' for 'python' succeeded. 'python37' is now active.
$ python --version
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
※pipenvやpoetryなどを使っても良い。

# OpenMX
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
# CrySPYのsetup
基本的に[マニュアル](https://tomoki-yamashita.github.io/cryspy/tutorial/01_install.html)通りインストールすれば良い。注意事項をメモ
CrySPY0.8.0を使うには
- find_wy
- COMBO
が必要。その前に必要なpythonパッケージをpipでインストール
## requirement
requirement.txtに
```
numpy >=1.10
scipy >= 0.16
Cython >= 0.22.1
pandas
pymatgen
```
と書いて
```shell script
$ pip install -U -r requirement.txt
```
とすれば一発で入る。  
※pymatgenのビルドが失敗した場合はpython-develを入れれば対応できる場合もある  
可視化のためにjupyter notebookもinstall
```shell script
$ pip install notebook
```
## find_wy
マニュアル通りインストールすれば良い
https://tomoki-yamashita.github.io/cryspy/tutorial/01_install.html  
エラーが出るのでコードを修正する必要がある
https://tomoki-yamashita.github.io/CrySPY/installation.html#installation-of-find-wy
## COMBO
[tsudalab/combo3](https://github.com/tsudalab/combo3)から
```shell script
git clone https://github.com/tsudalab/combo3.git
```
Warning:[tsudalab/combo repository](https://github.com/tsudalab/combo) is the python2 version.
You can't install combo3 by
```shell script
git clone https://github.com/tsudalab/combo.git
```
インストールしたら
```shell script
cd combo3
python setup.py install
```
を実行しセットアップ。**一度ディレクトリの外に出てから**
```shell script
(python) import combo
```
を実行し、エラーが出てないことを確認したら成功
