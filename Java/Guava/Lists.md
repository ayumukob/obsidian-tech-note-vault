Listに関連したユーティリティクラス。
## インスタンス生成
### 空のリストで初期化
古いJavaだったらGuavaの方が簡潔だったけど、今はもうどちらも変わらん。
```java
//Java
List<String> list = new ArrayList<>();

//Guava
List<String> list = Lists.newArrayList();
```
### 要素有りで初期化
入れるべき要素が事前にある場合(UTでよくあるパターン)に、空リストにaddしていくのはコードが長くなるし、めんどい。
そこでGuavaを利用するとスッキリ記述できる。
```java
//Java
List<String> list = new ArrayList<>();
list.add("a");
list.add("b");
list.add("c");

//Guava
List<String> list = Lists.newArrayList("a", "b", "c");
```
#java/guava