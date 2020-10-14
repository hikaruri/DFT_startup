# DFT_startup
自分/研究室用メモ。Macの環境構築。
- 最低限の設定
- OpenMX
- CrySPY
- soiap
- Quantum Espresso
# 最低限の設定
- パッケージ管理ソフト(macports)
- C/C++/fortranコンパイラ
- gnuplot
## 下準備
Xcodeと[Command line tools](https://developer.apple.com/download/more/?=command%20line%20tools)を入れる。　　
Terminalを立ち上げて
```shell script
$gcc -v
(中略)
Apple LLVM version 10.0.0 (clang-1000.11.45.5)
```
と出てきたら成功。
## パッケージ管理ソフト
[macports](https://github.com/macports/macports-base/releases/tag/v2.6.3)
環境は全て/opt/に入るので、最悪ここを消せば元に戻る(らしい)。
## C/C++/fortranコンパイラ
最新のgccを検索
```shell script
$ sudo port search gcc
```
gcc8を入れる
```shell script
$ sudo port install gcc8
```
インストールしたら確認
```shell script
$gcc -v
(中略)
gcc version 8.4.0 (MacPorts gcc8 8.4.0_0) 
```
と出たら成功
```shell script
$gcc -v
(中略)
Apple LLVM version 10.0.0 (clang-1000.11.45.5)
```
は失敗
## gnuplot
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
※OpenMPIはMKLと一緒に使えない、OpenMPI+scalapackを使いたい場合は(こちら)[https://qiita.com/hikaruri/items/0fa942c9eacb8930a792]
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
# 参考
[https://github.com/kaityo256/lab_startup](kaityo256/lab_startup)
