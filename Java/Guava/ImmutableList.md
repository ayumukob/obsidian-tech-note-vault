変更不可のListを生成できる。
よく使うArrays.asListは、実はjava.util.ArrayListのインスタンスではなく変更不可のList。
なのでImmutableListを利用する旨味はそんなにないかも。
Java9以降だとListにofというstaticメソッドができたのだが、こいつも変更不可List。
```java
//Java
List<String> list = Arrays.asList("a", "b", "c");

//Guava
List<String> list = ImmutableList.of("a", "b", "c");

//Java9
List<String> list = List.of("a", "b", "c");
```
#java/guava 