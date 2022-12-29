Javaのジェネリクスはコンパイル時にしか利用できず、実行時にはもう利用できない仕組みになっている。
よく安全ではない警告出るけど、あれは実行時に型情報が消去されるので安全ではないということ。

型を維持できないので、例えば以下のコードだと成功しちゃう。
```java
List<String> stringList = new ArrayList<>();
List<Integer> intList = new ArrayList<>();

boolean actual = stringList.getClass().isAssignableFrom(intList.getClass());

assertThat(actual, is(true));
```

で、Javaの標準的なリフレクションAPIを使えばメソッドやクラスの型の検出はできて、例えば、`List<String>`を返却するメソッドがあればリフレクションAPIで戻り値の型である`List<String>`を表す`ParameterizedType`を取得できる。

**TypeTokenはこの仕組みを利用して、ジェネリクスの操作を簡単にしてくれるクラス。**

具体的にいうとジェネリクスを持つ`Type`で`Class`しか利用できなかった操作もできるようにするもの。より便利な追加機能もある。
FW利用のために使っていたが改めて何者か調べると全然わかってなかった。
正直リフレクションをあまり利用してなくて、よくわかってないことが多いのでリフレクションについてはいつか勉強してまとめたい。

TypeTokenで型が何か判断できるので先程の例の処理もちゃんと判断できるようになる。
```java
TypeToken<List<String>> stringListTypeToken = new TypeToken<List<String>>() {}
TypeToken<List<Integer>> integerListTypeToken = new TypeToken<List<String>>(){}

boolean actual = stringListToken.isSubtypeOf(integerListTypeToken);

assertThat(actual, is(false));
```
isSubtypeOfはこの型が指定された型のサブタイプかどうかを判断してくれるのでfalseとなる。

もうすでに出てしまっているがJavaのリフレクションAPIを知ってないと読めないので、とりあえず今回の記事内でわからないメソッドたちはまとめておく。
- isAssignableFrom
このクラスまたはインタフェースが、指定されたクラスまたはインタフェース(親含む)と等しいかどうか
- getGenericReturnType
このメソッドの仮の戻り値の型を表すTypeを取得
- getTypeParameters
ジェネリック宣言で宣言された型変数を表すTypeVariableオブジェクトの配列を宣言順に取得
##  インスタンス取得
TypeTokenのインスタンスを取得する方法は3つ
- Typeをラップする方法　
  ```java
  TypeToken.of(method.getGenericReturnType()) 
  ```
- サブクラス(普通は匿名状態)でジェネリクスをキャプチャする方法
  ```java
  new TypeToken<List<String>>() {}
  ```
  正直何言ってんだ？って感じだが恐らくStringがサブクラス(どんなクラスでもObjectのサブクラス)って解釈してるけど合ってんのかな？？自信ねぇ〜
例えば以下のコードだとT型をキャプチャしてるだけでStringは取り出せない。
  ```java
  public class Util {
    public static <T> TypeToken<List<T>> listTypeToken() {
      return new TypeToken<List<T>>() {};
    }
  }
  
  TypeToken<List<String>> stringListTypeToke = Util.<String>listTypeToken();
  ```
- 型変数を知っているコンテキストクラスで解決する方法
  ```java
  protected abstract class IKnowMyType<T> {
    TypeToken<T> typeToken = new TypeToken<T>(getClass()) {};
  }

  new IKnowMyType<String>() {}.typeToken
  ```
  このパターンは先ほどできなかったものの代替みたいな感じかな？
## 発展版
複数のジェネリクスを持つ複雑なTypeTokenを作成し、実行時にそれらの情報を取得することもできる。
```java
TypeToken<Function<Integer, String>> functionTypeToken
  = new TypeToken<Function<Integer, String>>() {};

TypeToken<?> actual = functionResultTypeToken
  .resolveType(Function.class.getTypeParameters()[1]);

assertThat(actual, is(TypeToken.of(String.class));
```
リフレクションがわからなすぎて辛すぎるが、getTypeParametersで取得したTypeVariableの2要素目はいわゆるRとなるので、それをresolveTypeに指定してあげるとそれに対応した部分のTypeTokenが取得できるみたい。
他にもMapのEntryもわかったりする。
```java
TypeToken<Map<String, Integer>> mapTypeToken
  = new TypeToken<Map<String, Integer>>() {};

TypeToken<?> actual = mapTypeToken
  .resolveType(Map.class.getMethod("entrySet")
  .getGenericReturnType());

assertThat(actual, is(new TypeToken<Set<Map.Entry<String, Integer>>>() {});
```
#java/guava 