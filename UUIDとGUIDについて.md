## まずUUIDについてのおさらい
- Universally Unique IDentifier
- RFC4122で仕様定義されている
- フォーマットとしては**XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX**となる
	- X部分は16進数
	- テストデータとか手で作るとかあまりないと思うが、8桁4桁４桁４桁１２桁と覚えとくと良いかも
	- ３グループ目の最上桁はUUIDのバージョンを示す(**XXXXXXXX-XXXX-1XXX-XXXX-XXXXXXXXXXXX**)
- 小文字で出力し、入力に関しては大文字でも小文字でも両方受け付けるべきという仕様
- バージョン4の場合でいうと、存在パターンはおよそ$$5.3×10^{36}$$
### UUIDの衝突
どのバージョンでも衝突の可能性は**ゼロではない**
確率としては
$$1-\exp\Big(-\text{\(\cfrac {n^2} {2^{127}}\)}\Big)$$
数式に表すと上の式らしいがよくわからんので、簡単にいうと「230京回UUIDを生成すれば重複する」らしい。
他にも「103兆個のバージョン4UUIDの中から重複するものを見つける確率が10億分の1」だったり、「100年間に毎秒10億のUUIDを生成し続けた後に、1つのUUIDを生成したらそのUUIDが過去のものと衝突する確率は50%」とのことなので通常は**無視しても構わないレベル**

### 各バージョンの詳細

|バージョン|詳細|
|--:|---|
|1|UUIDを生成するマシンのMACアドレスとナノ秒単位の時刻を利用したもの<br>注意点として生成方式からMACアドレスや生成時間を逆算できてしまうこと|
|2|バージョン1のUUIDの一部にPOSIX[^1]のユーザIDやグループIDを取り込んだもの|
|3|FQDNやURLなどの何らかの一意な名前を用いてMD5を使ってハッシュ計算した値を利用したもの<br>すでに一意であることがわかっており、それと1対1に対応するUUIDが必要な場合に使われる<br>一意な値であれば何でも良いが、FQDN、URL、オブジェクト識別子などがよく使われる<br>ただ、**MD5は安全性に欠けるため非推奨**|
|4|疑似乱数を利用したもの|
|5|バージョン3のハッシュ計算をSHA-1に変更したもの<br>こちらは**セキュリティ認証情報などの使用は非推奨**|
[^1]: Portable Operating System Interface for UNIXの略
IEEEによって定められた、UNIX系OSが備えるべきとされる仕様の標準規格
### よく使ってるJavaでの実装例
Maven Dependency
```xml
<dependency>
  <groupId>com.fasterxml.uuid</groupId>
  <artifactId>java-uuid-generator</artifactId>
  <version>4.0</version>
</dependency>
```
実装サンプル
```java
//バージョン1
UUID = Generators.timeBasedGenerator().generate();

//バージョン4
UUID = Generators.randomBasedGenerator().generate();
```
## ではGUID
- Globally Unique IDentifier
- Microsoft社が定義・実装したもの
- 一部のMicrosoftのドキュメントではUUIDと同義であるとされている
- もともとMicrosoftが使用していたUUIDの変種を指していたが、現在では**UUIDの代替名でしかない**
#uuid #guid