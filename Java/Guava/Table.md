2次元配列的な2つのキーを持つデータ構造を表現したクラス。
row、column、valueで構成される。
イメージは`Map<row, Map<column, value>>`のような感じ。
compositeなキーをMapのkeyに使ってるような場合は置き換えれる。
Multimapと同様にJavaだとかなり煩雑なロジックになるところをカバーしてくれる。
ちなみに3つ以上のキーを持つデータ構造についてはサポートされていない。
## インスタンス生成
Tableも性質によっていろいろ種類がある。
他にもある。
```java
//LinkedHashMap的なTable
Table<String, String, String> table = HashBasedTable.create();

//TreeMap的なTable
Table<String, String, String> table = TreeBasedTable.create();
```
## valueへのアクセス
row指定、column指定もできる。
```Java
Table<String, String, String> table = HashBasedTable.create();
table.put("1", "a", "data1");
table.put("1", "b", "data2");
table.put("2", "c", "data3");
table.put("2", "a", "data1");

//valueを取得
String value = table.get("1", "a");

//columnに紐付くrowとvalueのMapを取得
Map<String, String> rowValueMap = table.column("a");

//rowに紐付くcolumnとvalueのMapを取得
Map<String, String> columnValueMap = table.row("1");
```
## 要素の削除
1要素の削除になるので、rowとcolumnを指定する。
```java
table.remove("1", "a");
```
## Mapへの変換
TableからMapへの変換もめっちゃ楽
```java
Map<String, Map<String, String>> map =  table.columnMap();
```
## Tableに含まれているかの確認
rowとcolumnの組み合わせ、columnのみ、rowのみ、valueのみという４つのチェックがある。
```java
table.contains("1", "a");

table.containsColumn("a");

table.containsRow("1");

table.containsValue("data1");
```
#java/guava 