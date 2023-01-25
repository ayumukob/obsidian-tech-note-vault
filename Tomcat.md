## コンテキストパス
コンテキストパスの指定は2種類
- server.xmlで指定
- <コンテキスト>.xml(コンテキスト記述子)で指定
## Java設定
何も考えなければ環境変数を利用すれば良いが、他のアプリと変えたい場合は個別に設定が可能
- Catalina.sh
```
JAVA_HOME="<Javaインストールディレクトリ>"
JRE_HOME="${JAVA_HOME}/jre"
```
## Tips
- バージョン確認<br>Windows：`インストールディレクトリ/bin/version.bat`<br>その他：`インストールディレクトリ/bin/version.sh`
- [[TomcatでPOSTサイズが2Mを超えるとき]]
- [[Tomcat7.0.55以降でファイルアップロード時にエラーが発生する]]
#tomcat 