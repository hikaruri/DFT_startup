# 遺伝的アルゴリズム
遺伝的アルゴリズムによる結晶構造探索のチュートリアル
構造最適化にはOpenMXを利用する。
## 概要
そのうち詳しく書く
## 入力ファイル
[cryspy.in](./OMX_C16_EA/cryspy.in)とcalc_inディレクトリを用意
### cryspy.inについて
EAに関連するパラメータについて説明する
```cryspy.in
[basic]
algo = EA
tot_struc = 30

[EA]
n_pop = 10
n_crsov = 5
n_perm = 0
n_strain = 3
n_rand = 2
n_elite = 2
slct_func = TNM
t_size = 2
maxgen_ea = 3
```
## 各種パラメータの説明
