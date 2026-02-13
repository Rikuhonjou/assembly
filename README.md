# C言語とx86-64アセンブリとCASL2(COMET2)

## 単純な加算でどのように処理が違うか比較

### C言語
![difference](./img/C.png) </br>

### x86-64アセンブリ
![difference](./img/assembly.png) </br>

### CASL2
![difference](./img/casl2.png) </br>

## 見比べ
[C言語] </br>
int a,b; </br>
a = 5; </br>
b = 10; </br>
int sum = a + b; </br>
printf("%i \n", sum); </br>

[CASL2]</br>
LD GR1,0005 <- a = 5</br>
LD GR2,0010 <- b = 10</br>
ADDA GR1,GR2 <- GR1 = a+b </br>

C言語とCASL2では意味が見やすい </br>

C言語のソースファイルを[gcc -S -O0 -fno-pic -fomit-frame-pointer add.c]でコンパイルすると </br>
[x86-64] </br>
movl $5, 44(%rsp) </br>
movl $10, 40(%rsp) </br>
movl 44(%rsp), %edx </br>
movl 40(%rsp), %eax </br>
addl %edx, %eax </br>
movl %eax, 36(%rsp) </br>
レジスタが少ない/スタックを使って変数を置く </br>
printfなどはまた外部の話 </br>
また、これは最適化されていないアセンブリコードである </br>

### 最適化したものとの比較
C言語のソースファイルを[gcc -S -O2 -fno-pic -fomit-frame-pointer add.c]でコンパイルすると </br>
![difference](./img/assemblyOptimization.png) </br>

最適化前 </br>
call    __main </br>
movl    $5, 44(%rsp) </br>
movl    $10, 40(%rsp) </br>
movl    44(%rsp), %edx </br>
movl    40(%rsp), %eax </br>
addl    %edx, %eax </br>
movl    %eax, 36(%rsp) </br>
movl    36(%rsp), %eax </br>
movl    %eax, %edx </br>
leaq    .LC0(%rip), %rcx </br>
call    print </br>

最適化により、計算結果がコンパイル時に確定したため </br>
演算命令（addl）は削除されたが </br>
関数呼び出し規約に基づくスタック復帰処理（addq）は </br>
プログラムの正当性維持のため残ることを確認した。 </br>
printf("%i \n",15); </br>
として凝縮された </br>

## 総括
gccの最適化オプション（-O0 / -O2）によって生成される
アセンブリを比較したところ、
定数計算や中間変数が最適化により消去され、
「計算手順」ではなく「意味だけが残る」ことを確認した。

これはCASL2が常に「人間が理解しやすい計算過程」を
明示的に記述する言語であるのに対し、
実在CPU向けアセンブリは
実行効率を最優先に意味を再構成する点で性質が異なると感じた。

もっと付け加えると </br>
CASL2では、関数呼び出し規約や
レジスタ割り当て戦略、
スタックフレーム管理といった概念は抽象化されている。

実在のアセンブリと比較することで、
CASL2は「CPUの計算モデルを理解するための言語」であり、
実機アセンブリは「システムと協調して動作するための言語」である
という役割の違いを理解できた。
