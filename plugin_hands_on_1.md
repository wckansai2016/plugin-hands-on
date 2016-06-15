#1. プラグイン化のメリット / プラグインの原型をつくる

##プラグイン化することのメリット

* テーマが変わっても機能がそのままつかえる。
* functions.php にコードを蓄積していくとカオスになる → 機能ごとにプラグイン化すると管理が楽。
* OG設定など毎回使うようなものをプラグイン化しておくと使い回ししやすく作業の効率化に。


##プラグインの原型をつくる
WordPress にプラグインとして認識されるファイルをつくってみよう！

plugin-hans-on-sample というフォルダを作りその中に plugin-hans-on-sample.php というファイルを作る。  
plugin-hans-on-sample.php ファイルに以下を貼り付ける。  
「Plugin Name」だけの記入でもOKです。  

```
<?php
/**
 * Plugin Name: Plugin-hans-on-sample
 * ↑プラグイン名
 * Version: 0.1-alpha
 * ↑バージョン
 * Description: WCK2016 Plguin Hans-on Sample.
 * ↑概要
 * Author: WCK2016 Plguin Hans-on Team
 * ↑作者名
 * Author URI: https://2016.kansai.wordcamp.org/
 * ↑作者サイトアドレス
 * Plugin URI: https://github.com/wckansai2016/plugin-hands-on-sample
 * ↑プラグイン公開アドレス
 * Text Domain: plugin-hans-on-sample
 * Domain Path: /languages
 * @package Plugin-hans-on-sample
 */
```

plugin-hans-on-sample フォルダを WordPress の wp-content → plugins フォルダにおく。  
   
管理画面 > プラグイン の画面にプラグイン名やプラグイン概要などが反映される。  
「有効化」ボタンをクリックすれば、プラグインとして機能するように。  
  
functions.php に書いていくように hans-on-sample.php ファイルへコードを書いていけば WordPress へ反映されます。   
  
ブログなどでよくみかける「この記事は○分で読めます」という、本文の文字数によって何分くらいで読めるかのお知らせを表示するコードをこの plugin-hans-on-sample.php ファイルに書いてプラグインをつくってみましょう！

[ Next >>：2.ショートコードを使って「この記事は○分で読めます」プラグインをつくる](https://github.com/wckansai2016/plugin-hands-on/blob/master/plugin_hands_on_2.md) 