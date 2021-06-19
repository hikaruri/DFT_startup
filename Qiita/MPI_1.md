# OpenMPIで任意の並列数に対応させる方法(単純なfor文の場合)

## 単純なfor文と並列処理

並列処理の基本はfor文の繰り返しをプロセスで分散させることです。このとき、各プロセスに担当させる繰り返し回数を記述する必要が出てくるのですが、学部の頃の自分は

「各プロセスの繰り返し回数=総繰り返し数÷総プロセス数」

というように実装してしまい、**プロセス数が繰り返し数の約数のときのみしか正常に動作しない**コードを作ってしまいました。あとから考えれば約数でない任意の並列数のときも簡単に実装可能なのですが、案外この考え方は難しいのかと思い自分へのメモも兼ねて記事にしました。

※2018/6/1 2次元の場合のコードにミスがあった為修正

※2018/6/2 コメ欄で教えて頂いている通り、
```
iprocINI = ceil(myid * (double)N / numprocs)
```
とかにすれば初期値の条件分岐を回避出来たのである....自戒の為、ダサい条件分岐は残しておく。

##　考え方
各プロセスの繰り返し回数は

1. 総繰り返し数÷総プロセス数 
2. 総繰り返し数÷総プロセス数+1

の二通りの場合で事足りることに注目します。もっとテクニカルな方法はあると思いますが、単純な繰り返しの場合はこれで問題ないでしょう。

## 準備
OpenMPI、C言語で実装しました。
[MPIの環境構築や基本コマンドのまとめ](https://qiita.com/kkk627/items/49c9c35301465f6780fa)
この記事を参考に、MPI_InitとかMPI_Finalizeを書いておきます。

## 例題1 (1次元の場合)
for文の基本例題
$$\sum_{i=1}^{N}i$$
を並列計算します。

```C:MPIsample1.c
#include <stdio.h>
#include "mpi.h"
#define Host_ID       0   

int main(int argc, char *argv[]){
  int myid, numprocs;           

  MPI_Init(&argc, &argv);   

  MPI_Comm_rank(MPI_COMM_WORLD, &myid);   // ランク数の取得
  MPI_Comm_size(MPI_COMM_WORLD, &numprocs);   // 全プロセス数の取得

  int N;
  N = 11;

  int iproc, Nproc, iprocINI, iprocEND;
  int Sumproc, Sum;

  /* 各プロセスの繰り返し数  */
  if ( myid < N % numprocs ){
    Nproc = N / numprocs + 1;
  }
  else{
    Nproc = N / numprocs;
  }
  
  /* 各プロセスの開始点 */
  if ( N % numprocs == 0 ){
    iprocINI = myid * Nproc;
  }
  /* 割り切れない場合 */
  /* +1をした部分まで   */
  else if ( myid < N % numprocs ){
    iprocINI = myid * Nproc;
  }
  /* +1をした部分としてない部分の境界*/
  else if ( myid == N % numprocs ){
    iprocINI = myid * ( Nproc + 1 );
  }
  /* +1をしてない部分*/
  else{
    iprocINI = N % numprocs + myid * Nproc;
  }

  iprocEND = iprocINI + Nproc;

  printf("myid = %d, iprocINI=%d, iprocEND=%d\n",myid, iprocINI, iprocEND);

  /* シグマの並列計算 */
  Sumproc = 0; Sum = 0;
  for ( iproc = iprocINI; iproc < iprocEND; iproc++){
     Sumproc += ( iproc + 1 );
  }
  
  MPI_Barrier(MPI_COMM_WORLD);
  
  MPI_Reduce(&Sumproc, &Sum, 1,MPI_INT,MPI_SUM,Host_ID,MPI_COMM_WORLD);
  
  MPI_Barrier(MPI_COMM_WORLD);

  if (myid == Host_ID)
    printf("Sum = %d\n", Sum);


  MPI_Finalize();   // MPIの終了処理
  return 0;
}
```
多分、各プロセスでの開始点の条件分岐はもっと賢い書き方があると思います、誰か教えて下さい。

## 例題2 (2次元の場合)
$$\sum_{i=1}^{N}\sum_{j=1}^{M}ij$$
ループを一次元にして対応します。

```C:MPIsample2.c
#include <stdio.h>
#include "mpi.h"
#define Host_ID       0   

int main(int argc, char *argv[]){
  int myid, numprocs;           

  MPI_Init(&argc, &argv);   

  MPI_Comm_rank(MPI_COMM_WORLD, &myid);   // ランク数の取得
  MPI_Comm_size(MPI_COMM_WORLD, &numprocs);   // 全プロセス数の取得

  int N, M;
  N = 3;
  M = 2;

  int iproc, Nproc, iprocINI, iprocEND;
  int i,j;
  int Sumproc, Sum;
  int Summesh = N * M;

  /* 各プロセスの繰り返し数  */
  if ( myid < Summesh % numprocs ){
    Nproc = Summesh / numprocs + 1;
  }
  else{
    Nproc = Summesh / numprocs;
  }
  
  /* 各プロセスの開始点 */
  if ( Summesh % numprocs == 0 ){
    iprocINI = myid * Nproc;
  }
  /* 割り切れない場合 */
  /* +1をした部分まで   */
  else if ( myid < Summesh % numprocs ){
    iprocINI = myid * Nproc;
  }
  /* +1をした部分としてない部分の境界*/
  else if ( myid == Summesh % numprocs ){
    iprocINI = myid * ( Nproc + 1 );
  }
  /* +1をしてない部分*/
  else{
    iprocINI = Summesh % numprocs + myid * Nproc;
  }

  iprocEND = iprocINI + Nproc;

  /* シグマの並列計算 */
  Sumproc = 0; Sum = 0;
  
  for ( iproc = iprocINI; iproc < iprocEND; iproc++){

        i =  iproc       % N ;
        j = (iproc - i)  / N ;

     Sumproc += ( i + 1 ) * ( j + 1 );
  }
  
  MPI_Barrier(MPI_COMM_WORLD);
  
  MPI_Reduce(&Sumproc, &Sum, 1,MPI_INT,MPI_SUM,Host_ID,MPI_COMM_WORLD);
  
  MPI_Barrier(MPI_COMM_WORLD);

  if (myid == Host_ID)
    printf("Sum = %d\n", Sum);


  MPI_Finalize();   // MPIの終了処理
  return 0;
}
```

## まとめ

単純なfor文を条件分岐を用いて任意の並列数に対応させることが出来ました。もっと簡潔に書く方法がありそうですが、取りあえずはこれで良いとしましょう。
