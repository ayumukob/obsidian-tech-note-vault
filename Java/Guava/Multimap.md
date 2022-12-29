keyに対して複数のデータを持つMap。
結構便利でそれなりに使ってる。
イメージ的には`Map<K, List<V>>`みたいな感じ。
この形式のMapを扱うと以下の例のようにかなり煩雑になってしまう。
ただ、Java8からは簡潔に書けるようになってはいる。
```java
//Java 超雑な作り
Map<String, List<String>> map = new HashMap<>();
if(!map.containsKey("1")) {
  map.put("1", new ArrayList<>());
}
List<String> list1 = map.get("1");
list1.add("a");

if(!map.containsKey("1")) {
  map.put("1", new ArrayList<>());
}
List<String> list2 = map.get("1");
list2.add("b");

if(!map.containsKey("1")) {
  map.put("1", new ArrayList<>());
}
List<String> list3 = map.get("1");
list3.add("c");

if(!map.containsKey("2")) {
  map.put("2", new ArrayList<>());
}
List<String> list4 = map.get("2");
list4.add("a");

if(!map.containsKey("3")) {
  map.put("3", new ArrayList<>());
}
List<String> list5 = map.get("3");
list5.add("a");

//Java8
Map<String, List<String>> map = new HashMap<>();

List<String> list1 = map.computeIfAbsent("1", k -> new ArrayList<>());
list1.add("a");

List<String> list2 = map.computeIfAbsent("1", k -> new ArrayList<>());
list2.add("b");

List<String> list3 = map.computeIfAbsent("1", k -> new ArrayList<>());
list3.add("c");

List<String> list4 = map.computeIfAbsent("2", k -> new ArrayList<>());
list4.add("a");

List<String> list5 = map.computeIfAbsent("3", k -> new ArrayList<>());
list5.add("a");
```
では、Guava。
```java
//Guava
Multimap<String, String> multiMap = ArrayListMultimap.create();
multiMap.put("1", "a");
multiMap.put("1", "b");
multiMap.put("1", "c");
multiMap.put("2", "a");
multiMap.put("3", "a");
```
単純にMapにputする感覚でListの要素を追加できてかなり楽。
Java8パターンは1行で記述できるのでこれだけだとあまり変わりがないが、Multimapは**valueのCollectionへの操作が非常に楽に記述できることがすごく強力。**
## インスタンス生成
valueの性質によっていろいろ種類がある。
他にもある。
```java
//HashMapのvalueがArrayListのMultiMap
Multimap<String, String> multiMap = ArrayListMultimap.create();

//ハッシュテーブルを利用したMultiMap
Multimap<String, String> multiMap = HashMultimap.create();

//LinkedHashMap的なMultiMap
Multimap<String, String> multiMap = LinkedHashMultimap.create();

//TreeMap/Set的なMultiMap
Multimap<String, String> multiMap = TreeMultimap.create();
```
## valueへのアクセス
普通にvalueを取得する方法のほかに、所謂entrySetのようなentries()がある。
普通のMap場合はkeyとvalueの組み合わせのまんまアクセスできるが、Multimapの場合はvalue側も分解され1つの要素となる。
```java
Collection<String> collection = multiMap.get("key");

//Java
Set<Entry<String, Collection<String>>> set = map.entrySet();
//中身としてはこんな感じ
//[{key:1, value:[a, b, c]}]

//Guava
Collection<Map.Entry<K,V>> collection = multiMap.entries();
//中身としてはこんな感じ
//[{key:1, value:a}, {key:1, value:b}, {key:1, value:c}]
```
## 要素の削除
普通のMapだとremoveするとkeyに紐付くvalueが削除されるが、Multimapのremoveの場合はkeyとvalueの要素を指定するので1Entryだけ削除される。Mapのremoveと同等はなのはremoveAllとなる。
```java
multiMap.remove("1", "b");

multiMap.removeAll("1");
```
## Mapへの変換
MultimapからMapへの変換はめっちゃ楽。
```java
Map<String, Collection<String>> map = multiMap.asMap();
```
## multiMapに含まれているかの確認
containsKeyはMapと同様だが、containsValueは違いvalueの全要素と比較してくれる。またcontainsEntryというものも存在し、keyとvalueのペアでチェックしてくれる。
```java
multiMap.containsKey("1");

multiMap.containsValue("b");

multiMap.containsEntry("2", "b")
```
## keyに紐づくvalueの要素数を取得
いちいちvalueを取得してsize確認しなくても良い
```java
Multiset<String> keys = multiMap.keys();
//中身としてはこんな感じ
//1件しか入っていないキーについてはキー情報しか出力されない
//[1 x 3, 2, 3]
```
#java/guava 