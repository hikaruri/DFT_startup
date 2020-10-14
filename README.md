# DFT_startup
自分/研究室用メモ。Macの環境構築。
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
# 参考
[https://github.com/kaityo256/lab_startup](kaityo256/lab_startup)
