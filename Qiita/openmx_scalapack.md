# OpenMX3.9をMacBook proでmakeする(macportsでScaLAPACK+openMPI)
## OpenMX3.9がmake出来ない
[OpenMXのサイト](http://openmx-square.org/)
hikaruriがトポロジカル不変量の計算コードでcontributeしたOpenMX3.9がリリースされました。
早速自分のMacBookProでも使おうと思い、ver. 3.7と3.8と同じmakefileでmakeしようとした所、どうやら上手くいかない。原因は今回のバージョンからScaLAPACKが必要らしく導入する必要があるらしい。
[MKL](https://kygraphy.com/ja/c-cpp/intel-mkl-on-mac-2/)を入れるのが一番簡単で確実らしいが、MPICHにしか対応してないようだ。
MacPorts+OpenMPIを使って(手軽に)導入したときの方法をメモ。
## 環境
Mojave 10.14.4
MacPorts 2.6.2
gcc 8.3.0
openmpi-gcc8 @4.0.1_0+fortran

## ScaLAPACKを導入
デフォルトだとMPICHを指定するようなので、variantsを指定するのを忘れないように

```
port install scalapack +gcc8 +openblas +openmpi
```
Buildする場所で失敗(わざと失敗させる)。
[Mac portでのOpenMPIを使ったScaLAPACKのインストール](https://github.com/t-sakashita/rokko/issues/338)
にあるように、MPIの部分でインストールに失敗するので、パッチを充てる必要がアリ。パッチファイルをダウンロードして、インストール中のscalapack-2.0.2のディレクトリを探す。パッチコマンドをscalapack-2.0.2の上で実行。

```
patch -p0 < ~/scalapack-2.0.2-patch.txt 
```
そしてもう一回

```
port install scalapack +gcc8 +openblas +openmpi
```
を実行してScaLAPACKが入ります。

追記(2020/3/17)
ld64が必要になる場合もある
https://github.com/wistaria/MateriAppsInstaller/issues/66

## OpenMXのmake
openmx3.9のmakefileのLIBに-lscalapackを付け加えると無事にmakeが通ります。

```
CC = mpicc -O3 -fopenmp -I/opt/local/include -I/usr/include
FC = mpif90 -O3 -fopenmp -I/opt/local/include -I/usr/include
LIB = -L/opt/local/lib -lfftw3 -llapack -lblas -lgfortran -lmpi_mpifh -lscalapack
```

## 終わりに
1. OpenMXのInstallのサポートページが欲しい
1. 単列版も出して欲しい
1. 自分のコードを使って欲しい