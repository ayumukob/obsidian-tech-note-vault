# Optionalについて
Java8で追加された値をラップしてくれるクラス。
こいつにラップすることでnull値を内包してくれるのでNullPointerExceptionの心配がなくなり、比較的きれいにnull制御ができたりする。
あまり使わないが、プリミティブを扱うOptionalクラスも別途存在する。
# よく使うメソッド
## of
Optionalを生成する。
もし引数がnullだった場合はNullPointerExceptionが発生する。
```java
List<String> list = Collections.singletonList("test");
Optional<String> optional = Optiona.of(list.get(0));
```
## ofNullable
Optionalを生成する。
こちらはnull対応版。
nullが渡ってきた場合はemptyとなる。
```java
List<String> list = Collections.singletonList(null);
Optional<String> optional = Optional.ofNullable(list.get(0));
```
## empty
空を表すOptionalを生成する。
```
Optional<String> optional = Optional.empty();
```
## isPresent
値を保持しているか。
```java
optional.isPresent();
```
## get
保持してる値を取得する。
もし値がなければNoSuchElementExceptionが発生するので、isPresentを事前にすることがほとんどかな。
```java
optional.get();
```
## orElse
値があれば取得し、なければデフォルト値を返却する。
```java
optional.orElse("empty");

optional.orElse(null);
```
## orElseGet
orElseにSupplierを渡した感じ。
この場合は**遅延評価**となる。
```
optional.orElseGet(()->1+1));
```
## orElseThrow
値があれば取得し、なければSupplierで渡したExceptionとなる。
```java
optional.orElseThrow(Exception::new);
```
## ifPresent
Consumerを渡して、値があればその値で処理を行う。
```java
optional.ifPresent(System.out::println);
```
## map
値を変換する。
値がない場合はemptyを返却する。
また、計算した結果がnullとなった場合もemptyとなる。
```java
Optional<Integer> optionalInt = optionalStr.map(t->Integer.parseInt(t));
```
## filter
条件を満たしていれば、自分自身を返却し、満たしてなければemptyを返却する。
元々emptyの場合は常にemptyを返却する。
```java
Optional<String> nameOptional = optionalStr.filter(t->t.equals("ayumukob"));
```
## stream
普段Java8なので使えなてないが、Java9以降にバージョンアップしたら使いたいと思ったメソッド。
値があれば、その値を持つStreamにしてくれて、なければ空のStreamにする
```java
Stream<String> stream = optional.stream();
```
## ifPresentOrElse
こちらも使えてないメソッド。これもJava9から使える。
値がない場合も処理するifPresentって感じ。
```java
optional.ifPresentOrElse(System.out::println, ()->System.out.println("nullだよ。"));
```
# 注意点
**前段が長くなったが、今回書きたかった部分はここ！！**
Intellijを使って実装してるとこんな警告を見る事があった。
```
'Optional<String>' used as type for parameter 'optional' 
Inspection info: Reports any cases in which java.util.Optional<T>, java.util.OptionalDouble, java.util.OptionalInt, java.util.OptionalLong, or com.google.common.base.Optional are used as types for fields or parameters.
Optional was designed to provide a limited mechanism for library method return types in which a clear way to represent "no result" was needed.
Using a field with the java.util.Optional type is also problematic if the class needs to be Serializable, as java.util.Optional is not serializable.
```
Optionalクラスは戻り値の型について「結果がない」ことを明確に表現する限定的な仕組みを提供するために設計されたという前提があり、戻り値ではなくクラスのフィールドや引数に利用するとそれに違反することなる。またシリアライズの関係でもOptionalはシリアライズできないらしいのでフィールドに存在しているとよくないと言っている。
:::message alert
つまり、Optionalは以下についての利用は推奨されていない！！
- クラスのフィールド
- メソッドの引数
:::
#java 