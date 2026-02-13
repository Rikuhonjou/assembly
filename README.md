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

C言語のソースファイルを[gcc -S -fno-pic -fomit-frame-pointer add.c]でコンパイルすると </br>
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
![difference](./img/assemblyOptimization.png) </br>

