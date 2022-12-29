クラスパスにあるリソースを操作するためのユーティリティ。
ファイル生成のためのURI生成に利用したりしている。
## そのリソースネームのURLを返却
```java
Resources.getResource(/sample/json.conf)
```
## URLから全ての文字列を読み込む
```java
String resourceString = Resources.toString(url, Charsets.UTF_8);
```
## URLから全てのバイトを読み込む
```java
byte[] resourceBytes = Resources.toByteArray(url);
```
## URLから全ての行を読み込む
```java
List<String> resourceLines = Resources.readLines(url, Charsets.UTF_8);
```
## Guavaでのファイル操作のためのオブジェクトByteSourceを取得
```java
ByteSource byteSource = Resources.asByteSource(url);
```
## Guavaでのファイル操作のためのオブジェクトCharSourceを取得
```java
CharSource textSource = Resources.asCharSource(url, UTF_8);
```
ファイル操作に関してはいつか書くかも。
#java/guava 