## プロキシ
基本プロキシ設定は`settings.xml`に記述するが、settings.xmlに記述されたプロキシ設定は1つしか有効にならないという挙動となる。
基本的に1つで大丈夫なことも多いと思うが、httpとhttpsを併用しなければならない場合などは困ってしまう。そういった場合は環境変数`MAVEN_OPS`にVMの起動オプションを設定しておけば複数のプロキシが有効になる
```
-Dhttp.proxyHost=<host> -Dhttp.proxyPort=<port> -Dhttps.proxyHost=<host> -Dhttps.proxyPort=<port>
```
## HTTP通信
maven 3.8.1からhttpへの通信が塞がれた
それ以降のクライアントを利用する場合リポジトリのURLなどをhttpsにする必要が出てくる
また、Maven Centralもhttpでは接続できなくなっている
## Tips
[[Maven Tips]]
## プラグイン
- [[Surefire]]
- [[Failsafe]]
#maven 