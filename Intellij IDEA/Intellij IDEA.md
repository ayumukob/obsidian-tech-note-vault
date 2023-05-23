## インストール
今からインストールするなら`Toolbox`経由で入れる
これで違うバージョンのIntellijも入れれるしアプデ管理もできる
インストール先はToolboxのインストール先の中になる
## 実行/デバッグ構成
起動前
- Make<br>英語だとbuildなのでわかりづらいが、モジュールをビルドする
- プロジェクトのビルド<br>プロジェクト全体をビルドする
## [[Subversion]]
svn 1.7以降がサポートされている
ただ警告は出てしまうがそれ以前のsvnも利用はできる
## [[Maven]]
Toolbox経由の場合、bundleされているMavenの場所は`AppData¥Local¥JetBrains¥Toolbox¥apps¥IDEA-C¥ch-0¥223.8214.52¥plugins¥maven¥lib¥maven3¥bin`
直接入れてる場合はインストールディレクトリの`plugins¥maven¥lib¥maven3¥bin`あたりを見とけば見つけられそう
## CheckStyle
Eclipseと同じようにCheckStyleやりたい場合は`CheckStyle-IDEA`プラグインをインストールすれば良い。
正直、[[Maven]]のプラグインが一番スマートだとは思うが。。。
## [[Tomcat]]
本来Tomcatを使いたいならUltimate版使えって話だがCommunityでどうしてもやりたい場合は機能は劣るが一応可能で、[[Smart Tomcat]]プラグインをインストールすれば良い
似たような`Tomcat runner`ってプラグインもあるけど、これはJava11以上からしか使えない
で、これも正直[[Maven]]プラグインがスマートだったりするのでは？
## コンソールの文字化け
カスタムVMオプションに`-Dfile.encoding=UTF-8`を追加すると解決する
`メニュー`->`ヘルプ`->`カスタムVMオプションの編集`  
#intellij
## 設定
- プロジェクトツリーで選択したファイルをエディタに表示する(クリックで表示)
	- Auto Scroll to Sourceにチェック