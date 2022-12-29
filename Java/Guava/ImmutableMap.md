変更不可のMapを生成できる。
利用したことある内容は[[ImmutableSet]]と同じ感じ
```java
//Guava
Map<String, String> map = ImmutableMap.of("1", "data1", "2", "data2", "3", "data3")

//Java9
Map<String, String> map = Map.of("1", "data1", "2", "data2", "3", "data3")
```
#java/guava 