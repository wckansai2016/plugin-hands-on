# WordPress プラグイン作成のハンズオン
##「この記事は○分で読めます」プラグインをつくってみよう！

WordCamp Kansai 2016 のプラグイン作成ハンズオンの資料です。

### ![](images/title.png)講師
[宮崎 さおり](https://profiles.wordpress.org/saorimiyazaki/)　/　[Toro_Unit](https://profiles.wordpress.org/toro_unit)　/ [Takayuki Miyoshi](https://profiles.wordpress.org/takayukister/)　/　[Hinaloe](https://profiles.wordpress.org/hnle/)　/　[二神 暢彦](https://profiles.wordpress.org/n2gami)

### ![](images/title.png)このハンズオンの目的
* 機能をプラグイン化することのメリットを知ってもらう。
* 実際に簡単なプラグインを作ってプラグイン作成の工程を理解してもらう。
* ショートコードの使い方や、投稿記事の情報取得・扱い方など機能を実装する上で必要なテクニックを学習してもらう。
* フィルターフック・アクションフックを使って安全にカスタマイズする手法を知ってもらう。  
	:speech_balloon: フィルターフック・アクションフックをコピペではなく、理解して使うことでプラグイン作成の幅が広がります。


※このハンズオンでは PHP の変数や関数を使用してプラグインを作成していきますが、PHPの基礎的な記述方法などについては触れません。

### ![](images/title.png)目次

1. [プラグイン化のメリット / プラグインの原型をつくる](https://github.com/wckansai2016/plugin-hands-on/blob/master/plugin_hands_on_1.md)
+  [ショートコードを使って「この記事は○分で読めます」プラグインをつくる](https://github.com/wckansai2016/plugin-hands-on/blob/master/plugin_hands_on_2.md)
    + ショートコードについて知ろう
    + 「この記事は○分で読めます」を表示するにはどのような処理が必要?？
    + 計算した分数と定型文「この記事は○分で読めます」を合わせて表示する関数を作る
    + ショートコード化する 
+ [フィルターフックを使って「この記事は約○分で読めます。」を自動で表示してみよう](https://github.com/wckansai2016/plugin-hands-on/blob/master/plugin_hands_on_3.md)
    + フィルターフックとは
    + フィルターフックの使い方
    + フィルターフックで自動的に表示する機能を追加する
+ [アクションフックを使って「この記事は約○分で読めます」にスタイルをあててみよう](https://github.com/wckansai2016/plugin-hands-on/blob/master/plugin_hands_on_4.md)
    + アクションフックとは
    + アクションフックの使い方
    + 「この記事は約○分で読めます」にあてるスタイルシートをアクションフックで読み込む
+ [（当日追加分）ショートコードに引数を渡して表示する時間の単位を変えてみよう](https://github.com/wckansai2016/plugin-hands-on/blob/master/plugin_hands_on_5.md)
    + ショートコードの引数について
    + 表示する時間の単位を引数で指定できるようにする

### ![](images/title.png)今回作成するプラグインのサンプルコード
最終的にこのコードが完成できるよう進めていきます。

[wckansai2016/plugin-hands-on-sample](https://github.com/wckansai2016/plugin-hands-on-sample) :point_right: [plugin-hans-on-sample.php](https://github.com/wckansai2016/plugin-hands-on-sample/blob/master/plugin-hans-on-sample.php)
