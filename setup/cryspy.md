
# CrySPY setup
基本的に[マニュアル](https://tomoki-yamashita.github.io/CrySPY_doc/installation/)通りインストールすれば良い。

## Install0.8.0

CrySPY0.8.0を使うには
- find_wy
- pymatgen
- COMBO

が必要。

requirement.txtに
```
numpy >=1.10
scipy >= 0.16
Cython >= 0.22.1
pandas
pymatgen
notebook
```
と書いて
```shell script
$ pip install -U -r requirement.txt
```
とすれば一発で入る。  
※pymatgenのビルドが失敗した場合はpython-develを入れれば対応できる場合もある、共用計算機の場合はanacondaなどでインストールすると上手くいくときもある。

### find_wy

[マニュアル](https://tomoki-yamashita.github.io/cryspy/tutorial/01_install.html)
通りインストールすれば良い

gfortranでコンパイルする場合、エラーが出るので[コードを修正する必要がある](https://tomoki-yamashita.github.io/CrySPY/installation.html#installation-of-find-wy)

### COMBO
[tsudalab/combo3](https://github.com/tsudalab/combo3)から
```shell script
git clone https://github.com/tsudalab/combo3.git
```
Warning:[tsudalab/combo repository](https://github.com/tsudalab/combo) is the python2 version.  

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

## Install0.9.0
CrySPY0.9.0を使うには
- pyXtal
- pymatgen
- COMBO

が必要。マニュアルの通り、pyXtalがpython3.9に対応していないのでpython3.8を使う

requirement.txtに
```
numpy >=1.10
scipy == 1.5.4
Cython >= 0.22.1
mpi4py >= 2.0
PyXtal >= 0.1.6
pymatgen == 2020.12.31
notebook >= 6.2.0
pandas >= 1.2.1
matplotlib >= 3.3.4
```
と書いて
```shell script
$ pip install -U -r requirement.txt
```
とすれば一発で入る。
※pymatgenのビルドが失敗した場合はpython-develを入れれば対応できる場合もある、共用計算機の場合はanacondaなどでインストールすると上手くいくときもある。

### COMBO
[tsudalab/combo3](https://github.com/tsudalab/combo3)から
```shell script
git clone https://github.com/tsudalab/combo3.git
```
Warning:[tsudalab/combo repository](https://github.com/tsudalab/combo) is the python2 version.  
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
