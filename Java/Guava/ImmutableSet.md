変更不可のSetを生成できる。
こいつはJava9以降のSet.ofが同じ挙動となる。
```java
//Guava
Set<String> set = ImmutableSet.of("a", "b", "c");

//Java9
Set<String> set = Set.of("a", "b", "c");
```
#java/guava 