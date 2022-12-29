整数を判断したいなぁと思ったときに見つけたユーティリティ。
Double型に関する計算を簡単にしてくれるもの。IntMathというユーティリティがあるのだが、内容はそれに類似するらしい。
## 数学的な整数かどうか判断する
```java
boolean actual1 = DoubleMath.isMathematicalInteger(5);
boolean actual2 = DoubleMath.isMathematicalInteger(5.2);

assertThat(actual1, is(true));
assertThat(actual2, is(false));
```
で、**数学的な**という部分が引っかかり色々調べたら、「非数 、無限大」という概念に関しても考慮した形らしい。正直システム要件的には問題なさそうにも思えたが、そんなに厳密は話でなくても問題なさそうだったし、非数とか何言ってんのかよくわからなくなった(笑)ので使用は見送った。
## 階乗計算
```java
double result = DoubleMath.factorial(5);
```
## 2の累乗かどうか
```java
boolean actual1 = DoubleMath.isPowerOfTwo(16);
boolean actual2 = DoubleMath.isPowerOfTwo(10);

assertThat(actual1, is(true));
assertThat(actual2, is(false));
```
## log2計算
```java
double result = DoubleMath.log2(4);
```
## アベレージ計算
```java
double result = DoubleMath.mean(30.1, 20.9, 10);
```
#java/guava 