
# CrySPY setup
基本的に[マニュアル](https://tomoki-yamashita.github.io/cryspy/tutorial/01_install.html)通りインストールすれば良い。

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

[マニュアル](https://tomoki-yamashita.github.io/cryspy/tutorial/01_install.html)
通りインストールすれば良い

gfortranでコンパイルする場合、エラーが出るので[コードを修正する必要がある](https://tomoki-yamashita.github.io/CrySPY/installation.html#installation-of-find-wy)

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
