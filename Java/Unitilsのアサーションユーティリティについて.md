# Unitilsって?
**UTを簡単にするライブラリ**
Junit、TestNGとか**テスティングフレームワークをより使いやすくする**目的で使う。
以下の機能を備えてる。
- 一般的なテストで利用できるユーティリティ
- Mockサポート
- 永続化層のテストサポート
- Springインテグレーションのサポート etc

ただ他のライブラリと組み合わせるとダブってる機能もかなりあるので自分はユーティリティしか使ってない。
Spring関連のサポートとか知らんかったし。。。
# 使い方
機能ごとのモジュールが存在し、必要なものを利用する。
モジュール一覧
- unitils-core
- unitils-testng
- unitils-database
- unitils-dbmaintainer
- unitils-dbunit
- unitils-easymock
- unitils-inject
- unitils-mock
- unitils-orm
- unitils-spring
- unitils-io
- unitils-test

以下はアサーションユーティリティを利用する場合。
Maven Dependency
```xml
<dependency>
    <groupId>org.unitils</groupId>
    <artifactId>unitils-core</artifactId>
    <version>3.4.6</version>
</dependency>
```
# アサーションユーティリティ
## リフレクションを利用したアサーション
### assertReflectionEquals
同値性チェックユーティリティでより簡単に検証できる。

例えば以下のクラスがあるとする。
```java
public class Sample {
  private final int number;
  
  private final String name;
  
  private final Map<String, Hoge> hogeMap;
  
  public Sample(int number, String name, Map<String, Hoge> hogeMap) {
    this.number = number;
    this.name = name;
    this.hogeMap = hogeMap;
  }
}

public class Hoge {
  private final String hogeStr;
  
  private final Integer hogeInt;
  
  public Hoge(String hogeStr, Integer hogeInt) {
    this.hogeStr = hogeStr;
    this.hogeInt = hogeInt;
  }
}
```
テストクラスでSampleクラスの同値チェックをしようとJunit4のassertThatで検証するとエラーとなってしまう。
```java
public class SampleTest {

  @Test
  public void test_sample() {
    Sample actual = new Sample(1, "sample", Collections.singletonMap("hoge1", new Hoge("hogeStr", 1)));
    Sample expected = new Sample(1, "sample", Collections.singletonMap("hoge1", new Hoge("hogeStr", 1)));
    
    assertThat(actual, is(expected));
  }
}
```
これがなぜ失敗しちゃうかというと、SampleクラスもHogeクラスもequalsメソッドが定義されていないため、Objectのequalsが実行され、**インスタンス比較となってしまうの**でactualとexpectedは別物となる。
なので、equalsを実装すりゃいいのだが、プロダクションコード的に同一性の担保が必要ではないクラスの場合、いちいち作るのもめんどい(まぁlombokとか使えばいいんだけど)。
そういう時に使うのがassertReflectionEquals。
```java
public class SampleTest {

  @Test
  public void test_sample() {
    Sample actual = new Sample(1, "sample", Collections.singletonMap("hoge1", new Hoge("hogeStr", 1)));
    Sample expected = new Sample(1, "sample", Collections.singletonMap("hoge1", new Hoge("hogeStr", 1)));
    
    assertReflectionEquals(expected, actual);
  }
}
```
assertReflectionEqualsは**再帰的に比較してくれる**ので、MapやListなどでもちゃんと比較してくれる。
また、プリミティブな値やそのラッパークラスの比較は`==`が利用されるため、例えば**1と1Lを比較すると一致する**点には注意。
さらに、**第一引数(期待値)のオブジェクトのプロパティ情報を元に、第二引数(実測値)の比較を行う**ので、例えば以下のようなクラスを比較すると、
```java
public class Huga extends Hoge{
  private final String hugaStr;
  
  public Hoge(String hogeStr, Integer hogeInt, String hugaStr) {
    super(hogeStr, hogeInt);
    this.hugaStr = hugaStr;
  }
}
```
**以下は成功**して、
```java
public class SampleTest {

  @Test
  public void test_sample() {
    Huga actual = new Huga("hogeStr", 1, "hugaStr")));
    Hoge expected = new Hoge("hogeStr", 1);
    
    assertReflectionEquals(expected, actual);
  }
}
```
**以下は失敗**するっていうのも注意ポイント
```java
public class SampleTest {

  @Test
  public void test_sample() {
    Hoge actual = new Hoge("hogeStr", 1);
    Huga expected = new Huga("hogeStr", 1, "hugaStr")));
    
    assertReflectionEquals(expected, actual);
  }
}
```
## ゆるやかなアサーション
### ReflectionComparatorMode
assertReflectionEqualsではオブションを指定することで、比較をゆるめることが可能。
1. **LENIENT_ORDER**
コレクション要素の順序を無視するオプション。
コレクションの**並び順がどうでも良い場合に利用**する。
```java
public class SampleTest {

  @Test
  public void test_sample() {
    List<String> actual = Arrays.asList("b", "a", "c");
    List<String> expected = Arrays.asList("a", "b", "c");
    
    assertReflectionEquals(expected, actual, ReflectionComparatorMode.LENIENT_ORDER);
  }
}
```
2. IGNORE_DEFAULTS
第一引数(期待値)のプロパティに設定した値にオブジェクトであればnull、プリミティブであれば0やfalseといったJavaのデフォルト値を設定した場合、比較対象から除外してくれるオプション。
基本あまり使わない気もするが、オブジェクトのプロパティの一部だけ検証したい場合などに利用する。
```java
public class SampleTest {

  @Test
  public void test_sample() {
    Sample actual = new Sample(1, "sample", Collections.singletonMap("hoge1", new Hoge("testStr", 1)));
    Sample expected = new Sample(0, "sample", Collections.singletonMap("hoge1", new Hoge(null, 1)));
    
    assertReflectionEquals(expected, actual, ReflectionComparatorMode.IGNORE_DEFAULTS);
  }
}
```
3. LENIENT_DATES
日付の比較時に存在確認はするけど実際の値自体の検証は無視してくれるオプション。
IGNORE_DEFAULTSを使いたくないけど、日付系のオブジェクトがある時に利用する。
```java
public class SampleTest {

  @Test
  public void test_sample() {
    Data actual = new Date(11111);
    Data expected = new Date();
    
    assertReflectionEquals(expected, actual, ReflectionComparatorMode.LENIENT_DATES);
  }
}
```
ただ、Date型に関していうと内部ではtransientのlong値で日付情報を保持しており、**assertReflectionEqualsではtransientのプロパティは評価されない**という挙動らしいので上記の例だとオプションがなくても成功してしまうことに注意。
### assertLenientEquals
LENIENT_ORDERとIGNORE_DEFAULTSがプリセットされたassertReflectionEquals。
## プロパティアサーション
### assertPropertyReflectionEquals
比較機能としてはassertReflectionEqualsと同じ。
２つのオブジェクトの特定のプロパティを比較するもの。
OGNL(Object Graph Navigation Language)という記述方式で指定する必要がある。
OGNLについてはまた別の機会にでも書こうかな。
```java
public class SampleTest {

  @Test
  public void test_sample() {
    Sample actual = new Sample(1, "sample", Collections.singletonMap("hoge1", new Hoge("hogeStr", 1)));
    
    assertPropertyReflectionEquals("number", 1, actual);
  }
}
```
ネストされたプロパティも検証可能。
```java
public class SampleTest {

  @Test
  public void test_sample() {
    Sample actual = new Sample(1, "sample", Collections.singletonMap("hoge1", new Hoge("hogeStr", 1)));
    
    assertPropertyReflectionEquals("hogeMap.hoge1.hogeStr", "hogeStr", actual);
  }
}
```
actualにコレクションを指定することもでき、指定されたプロパティはコレクションの各要素と比較される。
```java
public class SampleTest {

  @Test
  public void test_sample() {
    Sample sample1 = new Sample(1, "sample", Collections.singletonMap("hoge1", new Hoge("hogeStr", 1)));
    Sample sample2 = new Sample(2, "sample", Collections.singletonMap("hoge1", new Hoge("hogeStr", 1)));
    Sample sample3 = new Sample(3, "sample", Collections.singletonMap("hoge1", new Hoge("hogeStr", 1)));
    
    assertPropertyReflectionEquals("number", Arrays.asList(1, 2, 3), Arrays.asList(sample1, sample2, sample3));
  }
}
```
### assertPropertyLenientEquals
機能としてはassertPropertyReflectionEqualsと同じで、比較に関してはassertLenientEqualsベース。
#java/unitils
