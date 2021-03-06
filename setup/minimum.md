# minimum setup
Installしたいものリスト
- macports
- C/C++/fortran compiler
- gnuplot
- Python enviroment

## 下準備

Xcodeと[Command line tools](https://developer.apple.com/download/more/?=command%20line%20tools)を入れる。
Terminalを立ち上げて

```
$ gcc -v
(中略)
Apple LLVM version 10.0.0 (clang-1000.11.45.5)
```
と出てきたら成功(clangと出てくれば大丈夫)。

## macports
[macports](https://github.com/macports/macports-base/releases/tag/v2.6.3)
環境は全て/opt/に入るので、最悪ここを消せば元に戻る(らしい)。

```
$ sudo port selfupdate
```

を使う前に叩いておく。
※Homebrewを使っても良い

## C/C++/fortranコンパイラ
最新のgccを検索

```
$ sudo port search gcc
```
gcc8を入れる

```
$ sudo port install gcc8
```
インストールしたらselect

```
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

```
$gcc -v
(中略)
gcc version 8.4.0 (MacPorts gcc8 8.4.0_0) 
```
と出たら成功。  
※[gcc10の場合、OpenMXのコンパイルに失敗することがある](http://www.openmx-square.org/forum/patio.cgi?mode=view&no=2704)

```
$gcc -v
(中略)
Apple LLVM version 10.0.0 (clang-1000.11.45.5)
```
とclangのままだと失敗  
※Catalinaの場合はコンパイルの際に

```
fatal error: 'stdio.h' file not found
```
とエラーが出る場合があるので.zshrcに

```
export SDKROOT="$(xcrun --sdk macosx --show-sdk-path)"
```
と入れる必要がある
## gnuplot
### For Catalina User
For Catalina, macports version's gnuplot has [some bugs](https://qiita.com/_shin_/items/9d1482b7f21d7f2bc8c6).
Please install [the binary version](https://csml-wiki.northwestern.edu/index.php/Binary_versions_of_Gnuplot_for_OS_X).
### Others
macports経由で

```
$ sudo port install gnuplot
```

```
$ gnuplot
(中略)
Terminal type is now 'aqua'
gnuplot> 
```
と出れば成功。

## Python enviroment
### anacondaを使う場合(Easy)
anacondaのHPから[ダウンロード](https://www.anaconda.com/products/individual)
すぐにterminalでpythonが使えるように.bashrc(catalinaなら.zshrc)が書き換えられてるはず。

```
conda create --name env1
conda activate env1
```
でbase環境以外の仮想環境を作ることができる。CrySPY用の環境は全てココに入れたほうが良い(baseに入れてもCrySPYしか使わないのであればなんとかなる)

### virtualenvを使う場合
port経由でpython3とpipを入れる

```
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
※PATHが通っていない時は.bashrcや.zshrcに

```
export PATH=$PATH:/Users/sawahata/Library/Python/3.8/bin
```
と書く必要がある。  
Virtualenvで仮想環境を用意する準備

```
$ pip install virtualenv
$ virtualenv --version
virtualenv 20.0.28
```
仮想環境env1を作る
```
virtualenv env1
```
env1をactivate
```
source env1/bin/activate
```
activateしてからpip installをすることによってソースコード毎に違う環境で作業出来る。
CrySPY専用の環境もこれで作る。  
※pipenvやpoetryなどを使っても良い。
