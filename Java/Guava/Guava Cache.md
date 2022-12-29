Guavaでは簡単にインメモリキャッシュを利用できる。
**スレッドセーフ**なのでマルチスレッドでも問題なし。
## キャッシュの登録
- 自動的にキャッシュに登録
LoadingCacheインターフェースでキャッシュを操作していく。
Guava Cacheのドキュメントを見ていくとこちらが一般的なように見えるが、自分としてはどのようなユースケースでこれを利用していくのかあまり想像できていない。ランダム値とかをvalueとして入れるみたいな時は使えそうだけど。。。
詳しい人誰か教えて（苦笑
  ```java
  LoadingCache<String, String> loadingCache = CacheBuilder.newBuilder()
    .build(new CacheLoader<String, String>() {
      @Override
      public String load(String key) throws Exception {
        return key.toUpperCase();
      }
    });
  
  //cacheに存在しないため、ここでvalueが自動生成
  String value = loadingCache.get("key");
  //cacheから値を取得
  value = loadingCache.get("key");
  ```
  この例ではkeyを大文字にしたものが自動的に登録される。
コメントに書いてあるとおりファーストアクセスではデータが存在しないため、CacheLoaderのloadメソッドに記述した計算をし、結果をキャッシュに登録する。
loadメソッドはkeyが引数となり、別のデータを付与できないため、**keyから計算される値か、外からのデータが不要な値しか登録できない**(ここがそんなに用途がないと思える部分)。
- 手動でキャッシュに挿入
Cacheインターフェースでキャッシュを操作していく。
ただ、CacheインターフェースはLoadingCacheインターフェースの親なのでLoadingCacheでも以下の操作は可能。
自分はこちらの方を多用するが、一般的なのかは不明。
  ```java
  Cache<String, String> cache = CacheBuilder.newBuilder().build();
  
  //キャッシュの登録
  cache.put("key", "cacheData");
  //cacheからvalueを取得
  String value = cache.getIfPresent("key");
  ```
  シンプルにキャッシュの登録、取得ができる。
また、一気に複数の値も登録できる。
  ```java
  cache.putAll(ImmutableMap.of("key1", "data1", "key2", "data2", "key3", "data3"));
  ```
## 取得について
取得するメソッドは3つ存在する。
- get
LoadingCacheでkeyに対するvalueを取得する。
load時にエラーが発生すると、**ExecutionExceptionがthrowされる**。
- getUnchecked
LoadingCacheでkeyに対するvalueを取得する。
getとの違いは**ExecutionExceptionがthrowされないこと**。
CacheLoaderをlambdaを利用して生成もでき、その場合Exceptionは吐かれないのでgetメソッドを使う必要がないという感じ(使えないわけではない)。
  ```java
  //lambdaを利用したインスタンス生成
  CacheLoader.from((key) -> key.toUpperCase())
  ```
- getIfPresent
Cacheでkeyに対するvalueを取得する。
**紐づくvalueがあればその値を返却するし、なければnullを返却する**。
## 削除について
あまり利用しないと思うが、明示的に削除も可能
```java
//一つのエントリーを削除
cache.invalidate("key");

//複数のエントリーを削除
cache.invalidateAll(Arrays.asList("key1", "key2", "key3"));

//全てのエントリーを削除
cache.invalidateAll();
```
## nullの扱い
CacheLoaderパターンで、nullをloadしようとするとExceptionがthrowされてしまう。
あまりないのかもしれないが、nullをloadする必要がある場合はOptionalを活用する。
## Eviction
Guava Cacheでは大きく分けて3つのEvictionを設定できる。
CacheBuilderにEviction設定を付与することで利用できる。
- 保持数による破棄
登録されたエントリー数が指定した数値を超えた場合に古いものから破棄される。
  ```java
  Cache<String, String> cache = CacheBuilder.newBuilder().maximumSize(10).build();
  ```
- 重みによる破棄
重みという表現が正しいのか不明だが、メソッドを見る限りWeightということなので重みとしておく。
任意の何かを超えた場合に古いものから破棄される。
任意の何かというのはWeigherクラスで定義する。
  ```java
  //この例はキャッシュに登録されたvalueのバイト数が10バイトを超えた場合に古いものから破棄
  Cache<String, String> cache = CacheBuilder.newBuilder()
    .maximumWeight(10)
    .weigher(new Weigher<String, String>() {
      @Override
      public int weigh(String key, String value) {
        return value.getBytes().length();
      }
    }).build();
  ```
- 時間による破棄
判断基準となる時間が2つ存在し、それぞれその時間から有効期限が過ぎたものから破棄される。
  ```java
  //最後にアクセスしたタイミング
  Cache<String, String> cache = CacheBuilder.newBuilder()
    .expireAfterAccess(3, TimeUnit.HOURS)
    .build();

  //作成したタイミング
  Cache<String, String> cache = CacheBuilder.newBuilder()
    .expireAfterWrite(1, TimeUnit.DAYS)
    .build();
  ```
## GCに関連した設定
キャッシュデータを強参照じゃなくすことができる。
これによりGCされた時に破棄されやすくできる。
正直そこまで厳密にGC制御しなきゃいけないアプリ作ったことはないので、あまり使う機会はなさそう。
```java
//keyをWeakReferenceにすることで強参照が存在しなくなり、GCが発生したタイミングでkeyも破棄される
Cache<String, String> cache = CacheBuilder.newBuilder().weakKeys().build();

//valueをWeakReferenceにすることで強参照が存在しなくなり、GCが発生したタイミングでvalueも破棄される
Cache<String, String> cache = CacheBuilder.newBuilder().weakValues().build();

//valueをSoftReferenceにすることで弱参照よりは強い参照となり、メモリに余裕があればGCが発生しても破棄されない可能性がある
Cache<String, String> cache = CacheBuilder.newBuilder().softValues().build();
```
## キャッシュの更新
LoadingCacheパターンの場合のリフレッシュ方法は手動更新と自動更新の2種類存在する。
- 手動でキャッシュを更新
refreshメソッドを使うことで更新できる。
  :::message
  処理が完了するまでにキャッシュを取得した場合は古い情報が取得できる。
  :::
  ```java
  loadingCache.refresh("key");
  ```
- 自動でキャッシュを更新
refreshAfterWriteを事前に設定しておくことで自動更新が可能。
  :::message
  時間を指定している訳だが、この時間を超えた時点で更新が走るわけではなく、この時間を超えた時に改めてそのkeyに対する取得をした時に更新が走る。
  :::
  また、Evictionのexpire系に似ているが、あちらはデータが破棄されるが、こちらは古いデータを保持し続けている。
  ```java
  LoadingCache<String, String> loadingCache = CacheBuilder.newBuilder()
    .refreshAfterWrite(1, TimeUnit.MINUTES)
    .build(new CacheLoader<String, String>() {
      @Override
      public String load(String key) throws Exception {
        return key.toUpperCase();
      }
    });
  ```
## 削除時の追加処理
キャッシュからデータが削除されたときに、何らかのアクションを起こす必要がある場合に、RemovalListenerを利用して追加処理を行うことができる。
以下の例はEvictionで破棄された時にコンソールにキーを出力する処理。
```java
Cache<String, String> cache = CacheBuilder.newBuilder()
  .maximumSize(10)
  .removalListener(new RemovalListener<String, String>() {
    @Override
    public void onRemoval(RemovalNotification<String, String> notification){
        if (notification.wasEvicted()) {
          System.out.println(notification.getKey());
        }
    }
  }).build();
```
削除された原因はgetCauseメソッドで取得でき、
- COLLECTED(GCにて破棄)
- EXPIRED(有効期限切れ)
- EXPLICIT(削除した時)
- REPLACED(更新した時)
- SIZE(サイズ超過)

の5種類存在する。
## パフォーマンス計測
各種統計データを簡単に取得できる。
recodeStatsを設定しておき、statsメソッドで統計データを取得する。
:::message alert
ただし、これを使うとパフォーマンスが低下を招くらしい。
:::
```java
Cache<String, String> cache = CacheBuilder.newBuilder().recordStats().build();

//CacheStatusに情報が格納されている
CacheStats cacheStats = cache.stats();
```
取れるデータとしては以下。
それなりに使えるデータが多いと思うがパフォーマンス問題がどれほど影響するのかな？
- 平均ロード時間
- Eviction数
- ヒット数
- ヒットレート
- ロード数
- ロード時のエラー発生回数
- ロード時のエラーレート
- ロードに成功した回数
- ミスヒット数
- ミスヒットレート
- リクエスト数
- 総ロード時間
#java/guava 