## 利用方法
1. `設定`->`Tomcat Server`
2. 利用するTomcatを追加
3. `実行/デバッグ構成`->`実行構成の編集`->`新規構成の追加`->`Smart Tomcat`
4. 任意の名前を設定
5. `Tomcat Server`に利用するTomcatを指定
6. `Deployment directory`にwebappのpathを指定
7. `Context path`にアプリケーションのコンテキストパスを指定<br>デフォルト(初期設定)だと`/<module_name>`<br>空だとルートになる挙動
8. `Server Port`に実行するポートを指定<br>デフォルトだと`8080`
9. `Admin Port`に管理ポートを指定<br>デフォルトだと`8005`
10. `VM Options`に必要なVMオプションを指定
11. `Env Options`に必要な環境設定を指定
12. 実行orデバッグを実行
## 挙動
1. ビルドを実行
2. ビルドしたリソースでTomcatを実行
実行時のデータは`¥<ユーザ>¥.SmartTomcat`に作られる
その際、tomcatのconfのcopyを持ってきてsmart tomcat用の設定を生成する
その中で`server.xml`の一つ目の`Connector`のポートを`Server Port`に指定したポートに書き換える
`Context path`に指定した内容から`catalina/localhost`にcontextのxmlを自動的に生成する
`server.xml`だけで運用しているような古いアプリケーションだと上記の挙動から問題が起こるかも
## 使いにくいところ
JVMを指定できないので、プロジェクトで指定したJVMでしか実行できない
## 要調査
もしかしたらJRE指定できるかもしれない
`.idea/workspace.xml`の`RunManager`->`Tomcat7`の設定を強制的に書き換えたら行ける可能性ある
#intellij #tomcat 