# 必要な並列プロセスに対してのみ集団通信をする(コミュニケーター操作の初歩の初歩)
## はじめに
[前回の記事](https://qiita.com/hikaruri/items/eb2e5b0cae38f63c3ff2)で任意の並列数に対応させる方法について触れました。
このとき**必要な並列数をプロセス数が超えたとき**にゼロサイズ配列をMPI_Gatherしてしまい、計算が止まるというヤバいバグに遭遇しました
(詳しい説明はコード公開に合わせて追記します)。

それの回避方法として**並列計算に必要なプロセス間でのみ集団通信を行う**という方法をとれば良いことはわかりましたが、それを実現するにはコミュニケーター操作をする必要があるということがわかり、メモ代わりに記事を書きました。

※2018/6/7追記
MPI_Gathervを使えばゼロサイズ配列のGatherも回避出来るらしい....早く知りたかった....
[参考URL](https://stackoverflow.com/questions/31890523/how-to-use-mpi-gatherv-for-collecting-strings-of-diiferent-length-from-different)

## コミュニケーターとは？
**データのやりとりをするプロセスの集まり**をコミュニケーターって言うらしいです。

`MPI_Init`した後に与えられる
`MPI_COMM_WORLD` は全プロセスを指すコミュニケーター
`MPI_COMM_SELF`　は自分プロセスだけを指すコミュニケーター

グループとはまた違うらしいです。ぶっちゃけ良くわかってません。以下のリンクを読めば詳しいことはわかるのでそちらでお願いします。

参考リンク
1.[http://www.r-ccs.riken.jp/r-ccssite/wp-content/uploads/2016/03/Sp16_MPI_Tsujita_part1.pdf](http://www.r-ccs.riken.jp/r-ccssite/wp-content/uploads/2016/03/Sp16_MPI_Tsujita_part1.pdf)
2.[http://www.hpci-office.jp/invite2/documents2/mpi_20170911.pdf](http://www.hpci-office.jp/invite2/documents2/mpi_20170911.pdf)

## やり方
**必要なプロセスサイズのグループを作る → そこからコミュニケーターが作れる**

という段階を踏む必要があるらしい。

詳しくはリンクを参照
[http://www.hpci-office.jp/invite2/documents2/mpi_20170911.pdf](http://www.hpci-office.jp/invite2/documents2/mpi_20170911.pdf)

#例題
並列数3の計算において
２つのプロセスのコミュニケーター:```comm1```
を作るC言語コード(OpenMPI)です。

```C:MPIcomm1.c
#include <stdio.h>
#include "mpi.h"

#define Host_ID   0

int main(int argc, char *argv[])
{
  int N = 2;
  int i;
 
  int myid, numprocs;
  
  int a = 0;
  
  MPI_Init( &argc, &argv );

  MPI_Comm_size( MPI_COMM_WORLD, &numprocs );
  MPI_Comm_rank( MPI_COMM_WORLD, &myid );

  printf("myid = %d of %d\n", myid, numprocs);
 
 
  /* Create size N group     */
  MPI_Group MPI_GROUP_WORLD, group1; 
  MPI_Comm_group( MPI_COMM_WORLD, &MPI_GROUP_WORLD ); 
  int ranks[N];
  for ( i = 0; i < N; i++ )
    ranks[i] = i;
  MPI_Group_incl( MPI_GROUP_WORLD, N, ranks, &group1 ); 

  /* group -> comm */
  MPI_Comm comm1;
  MPI_Comm_create( MPI_COMM_WORLD, group1, &comm1);

  if ( myid == Host_ID )
    a = 1;
  
  MPI_Barrier(MPI_COMM_WORLD);

  /* Bcast in size N comm   */
  if ( myid < N )
    MPI_Bcast ( &a, 1, MPI_INT, Host_ID, comm1 );
  printf("myid = %d, a = %d\n", myid, a);

  MPI_Barrier(MPI_COMM_WORLD);

  if ( myid == Host_ID )
    a = 2;

  MPI_Barrier(MPI_COMM_WORLD);

  /* Bcast in MPI_COMM_WORLD */
 
  MPI_Bcast ( &a, 1, MPI_INT, Host_ID, MPI_COMM_WORLD );
  printf("myid = %d, a = %d\n", myid, a);


  MPI_Finalize();   
  return 0;
}
```
唯一注意したい場所は
コミュニケーター```comm1```間でのみ```MPI_Bcast```
する時は
```if ( myid < N )　MPI_Bcast ( &a, 1, MPI_INT, Host_ID, comm1 );```
として、**```comm1```に属さないプロセスでは実行しない**ようにする必要があります。
(これを忘れるとエラーが出ます)

```mpirun -np 3``` での実行結果例は

```C:result
myid = 0 of 3
myid = 1 of 3
myid = 2 of 3
myid = 2, a = 0
myid = 0, a = 1
myid = 1, a = 1
myid = 0, a = 2
myid = 1, a = 2
myid = 2, a = 2
```
となりました。
## まとめ
コミュニケーター操作を使って必要なプロセスに対してのみ集団通信を行うことが出来ました。マジで極めるともっと難しいことが出来て超効率の良い計算(インターコミュニケーターとかなんちゃらかんちゃら)が出来るらしいですが、エラー回避の為ならこの程度の知識で十分かもしれません。
