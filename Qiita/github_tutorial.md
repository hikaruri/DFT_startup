# 超超超超入門Git/Github講座-黒い画面ナシでファイルの更新履歴管理しよう-

## 概要
Githubを使って**ファイルの更新履歴管理**をする方法**だけ**を説明します。
Git機能を超簡単に使って勉強意欲を高めてもらうことを目的とした記事です。
詳しい説明を期待しないでください。
## はじめに
誰しもが通る道

```
修論.tex
修論_1.tex
修論修正版.tex
修論修正版(1).tex
修論_先生コメント.tex
修論最終.tex
修論rev.tex
修論_図rev.tex
syuron_final.tex
syuron.tex
Finalsyuron.tex
Final(2).tex
ほんとうにさいご.tex
ほんとうにさいご(1).tex
```
**最終版どれだよ！？！？**ってなりますよね。
こうなったとき、友人から
**「Gitを使ってバージョン管理をすれば良いんだよ！」**
ってドヤ顔で言われたことありませんか？僕は10回くらいあります。
それを言われるたびに

* 「Git バージョン管理」って検索
* 「git initって黒い画面に打ち込んで〜」「mergeが〜branchが〜」謎用語だらけ
* 勉強意欲が失せる
* 「時間があるときに勉強しよう」
* 結局勉強しない
* 誰もお前を愛さない

と悪循環ループ。僕は10回くらいこのループを繰り返しました。
目的を達成するためには**「ファイルの更新履歴の管理」**だけが出来れば良いのです。
そのやり方**だけ**を今回覚えましょう。**超簡単**です。

## まずはGithubに登録だ！
Githubのサイトにアカウントを作りましょう
https://github.com/
Githubってなに？ →　Gitが使える超便利な情強サイトです。
そもそもGitって? → 更新履歴が管理出来るらしい。
程度の認識で良いです。まずはメールアドレスとアカウント名を登録してください。
こんな感じでプランを選ばされます。無料プランを選びましょう(左側)。
<img width="719" alt="スクリーンショット 2020-04-12 3.43.08.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/238983/0554f861-69ac-7144-f408-8a12eabd7d3b.png">
使い方を聞かれます、適当に答えましょう。
<img width="621" alt="スクリーンショット 2020-04-12 3.43.25.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/238983/be0e23c5-cbe1-7a58-458f-1aabcb5b578c.png">
#リポジトリを作ろう
リポジトリとは？→作業フォルダのことです。
ホーム画面がこんな感じになると思います。緑色のCreate repositoryってボタンを押しましょう。
<img width="945" alt="スクリーンショット 2020-04-12 3.45.01.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/238983/f43d5344-bf6c-f3ba-797e-d8e2eb6e7f09.png">
こんな画面が出てくると思います(デモのため仮アカウントで作ってます)。

* リポジトリの名前(作業フォルダの名前)を決める → Syuronとかにしましょう
* Privateボタンを選択 → Publicにすると黒歴史修論が世に放たれます

これだけで大丈夫です。細かいところは気が向いたら調べましょう。
Create repositoryって緑色のボタンを押してください。
<img width="727" alt="スクリーンショット 2020-04-12 3.53.28.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/238983/f1f809e8-8b50-0966-5a59-f75c05530e4c.png">
#ファイルをアップロードしてみよう
リポジトリを作ったらなんだかよくわからない画面に遷移しましたね
(このページに書いてあるよくわからないコマンドを見て僕は一度絶望しました)。
水色の部分にある
**uploading an existing file** 
って文字を押してください
<img width="1001" alt="スクリーンショット 2020-04-12 3.59.12.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/238983/899b6da5-29d5-5dfd-f740-cd61ad676147.png">
ページが遷移しましたね。編集するファイルをこのページに**直接ドラックアンドドロップ**してみてください。ファイルをマウスで動かしてページの上に持ってくるだけです。
<img width="1014" alt="スクリーンショット 2020-04-12 4.10.45.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/238983/fc0c4bce-da6f-84f2-4b3c-6d7a13ccfc90.png">
アップロードされたらCommit changesって緑色のボタンを押しましょう。
Commitって？ → 気が向いたら自分で調べてください
<img width="1010" alt="スクリーンショット 2020-04-12 4.12.42.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/238983/ca168a0a-1272-7c6a-b075-f3dba918be8c.png">
ファイルのアップロードに成功しました！
## ここからが本番、ファイルの更新と履歴を見よう
さて、アップロードしたあとに指導教官から修論を真っ赤に添削されてファイルを更新する必要が....
Githubなら簡単、ただこのページに更新後のファイルを**直接ドラックアンドドロップ**するだけです！
<img width="1031" alt="スクリーンショット 2020-04-12 4.14.30.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/238983/a95b1e0a-3c9c-bf46-3f16-957d8cd5b89c.png">
Commit changesって欄に直した一言理由と詳しい内容を書いておきましょう。Commit changesの緑色のボタンを押してください。
<img width="1012" alt="スクリーンショット 2020-04-12 4.20.34.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/238983/3a6555cc-8506-10fb-6c56-e4745b302fac.png">
更新完了です。
さて、**更新履歴がちゃんと残ってるのでしょうか？**
ちゃんと**元の状態に復元出来るのでしょうか？**
Githubなら簡単に出来ます。Commitsのボタンを押してみましょう。
<img width="1011" alt="スクリーンショット 2020-04-12 4.22.45.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/238983/dae21a60-c4ba-3476-73c0-2688476fcfaf.png">
更新履歴っぽいページに移りましたね。
<img width="1018" alt="スクリーンショット 2020-04-12 4.27.12.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/238983/498c9151-5cc7-48d8-d6b1-587e378f48fd.png">
「図のラベル修正」って書いた部分を押してみましょう、テキストファイルの場合は差分が詳しく見られます。
texソースでレポートor論文を書いている場合やプログラミングのソースコードの場合だったら便利そうですね。
<img width="1014" alt="スクリーンショット 2020-04-12 4.32.06.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/238983/27970a20-3571-7e19-dc72-57e2405c892e.png">
最初の状態を復元してみましょう、「<>」ってなってるボタンを押しましょう。
なんか最初の状態に戻ってるっぽいですね。Clone or downloadって緑色のボタンを押すと、この地点でのファイル一同がダウンロード出来るっぽいです。
<img width="1014" alt="スクリーンショット 2020-04-12 4.35.01.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/238983/dfeb0320-5538-aa9d-759b-d90ba2f695cf.png">
ちゃんと最初の状態に戻っていますね。
<img width="337" alt="スクリーンショット 2020-04-12 4.40.22.png" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/238983/9d7799af-7b29-10c3-90cb-e98b434c3848.png">
Githubを使ってファイルのバージョン管理を楽チンにして圧倒的成長しましょう。
## 勉強意欲が湧いたら
Git/Githubの詳しい解説については<a href="https://backlog.com/ja/git-tutorial/">
サルでもわかるGit入門</a>
<a href="https://speakerdeck.com/kaityo256/starting-git">数値計算屋のためのGit入門 / Starting Git</a>
<a href="https://techacademy.jp/magazine/6235">今さら聞けない！GitHubの使い方【超初心者向け】</a>
とか参考にしてください。
本を買うのも良いと思います、僕が買った本はこれです。普通にわかりやすいです。
<a href="https://book.impress.co.jp/books/1114101005.php">Web制作者のためのGitHubの教科書 チームの効率を最大化する共同開発ツール</a>

