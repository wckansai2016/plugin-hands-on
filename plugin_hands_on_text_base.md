#プラグイン化することのメリット

* テーマが変わっても機能がそのままつかえる。
* functions.php にコードを蓄積していくとカオスになる→機能ごとにプラグイン化すると管理が楽。
* OG設定など毎回使うようなものをプラグイン化しておくと使い回ししやすく作業の効率化に。


#プラグインの原型をつくる
WordPressにプラグインとして認識されるファイルをつくってみよう！

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
↓  
これだけでOK。管理画面 > プラグイン の画面にプラグイン名やプラグイン概要などが反映されます。  
   
「有効化」ボタンをクリックすれば、プラグインとして機能するように。  
functions.php に書いていくように hans-on-sample.php ファイルへコードを書いていけば WordPress へ反映されます。   
プラグインの作成とてもカンタン！  
↓  
ブログなどでよくみかける「この記事は○分で読めます」という、本文の文字数によって何分くらいで読めるかのお知らせを表示するコードをこの plugin-hans-on-sample.php ファイルに書いてプラグインをつくってみましょう！


#ショートコードを使って「この記事は○分で読めます」プラグインをつくる
WordPress のショートコードという機能を使ってプラグインをつくります。

##ショートコードとは

投稿記事内で WordPress のテンプレートタグや PHP のコードを実行するための仕組みです。

通常 WordPress の投稿記事内ではテンプレートタグを使ったり PHP を実行させることはできません。  
しかし、あらかじめショートコードに実行させたいコードを登録しておけば、投稿記事内でそのコードを実行することができます。

例えば、shortcode-sample という名前のショートコードに、投稿した日にちを表示する関数 get_the_date() を登録したとします。  
投稿記事に[shortcode-sample]と[]でショートコード名を囲って記入すると投稿した日にちをショートコードを記入した箇所に表示できます。

```
[shortcode-sample]
```

囲む形のショードコードもあります。[shortcode-sample]ショートコードサンプル[/shortcode-sample] のように本文のテキストや画像などを囲んで、その囲んだものに対してなにかしらの加工をしたりできます。

```
[shortcode-sample]ショートコードサンプル[/shortcode-sample]
```

##ショートコードの登録の仕方

ショートコードを登録するには、add_shortcode という関数を使います。  
[関数リファレンス/add shortcode - WordPress Codex 日本語版](http://wpdocs.osdn.jp/%E9%96%A2%E6%95%B0%E3%83%AA%E3%83%95%E3%82%A1%E3%83%AC%E3%83%B3%E3%82%B9/add_shortcode)  
  
プラグインファイルへ add_shortcode( 'ショートコード名', '実行する関数名' ) と、ショートコード名・実行したい関数名を記述します。  
  
前項で例にした、投稿した日にちを表示する関数 get_the_date() を実行したい場合は以下のように記述。

```
function shortcode_sample_func() {
	return '<p>'.get_the_date().'</p>';
}
add_shortcode( 'shortcode-sample', 'shortcode_sample_func' );
```
   
※ショートコードで実行する関数には echo するような処理を入れてはいけません。echo はもちろんですが、the_date() など echo するような関数も使用してはダメです。意図した形で表示できなくなってしまいます。

###ショートコードの引数について

実行する関数に引数を渡して処理することも可能です。  
任意の引数を渡すにはショートコードを記入する際に以下のように記入します。

```
[shortcode-sample post_id="14"]
```

ショートコードに = で記入された値は、関数側の第一引数で連想配列で取得できます。
受け取ると、= 前の文字列が配列のキーに、= の後の文字列が値として配列に入っています。  

```
var_dump( $attr );
↓
array(1) { 
 	["post_id"]=> string(2) "14" 
}
```

使い方の例として…、
記事IDを post_id="14" と指定して渡し、  
投稿した日にちを表示する関数の引数にして、指定した記事の投稿日を表示するようにできます。 

```
function shortcode_sample_func( $attr, $content = '' ) {
	return '<p>'.get_the_date( '', $attr['post_id'] ).'</p>';
}
```

ショートコードの第二引数では、ショートコードで囲んだ部分のコンテンツを取得できます。

```
[shortcode-sample]ショートコードサンプル[/shortcode-sample]
```
↓  

```
var_dump( $content );
↓
string(33) "ショートコードサンプル" 
```

ここから本題…、
##「この記事は○分で読めます」を表示するにはどのような処理が必要か？

本文のテキストの文字数を数えて何分で読めるのかを算出する   
↓  
計算した分数と定型文「この記事は○分で読めます」を合わせて表示する  
↓  
ショートコード化する

###テキストが何分で読めるかを計算する関数を作る

文字列の塊が何分で読めるのかを計算する関数を作ります。  

```
function count_reading_minutes( $content ) {
	// ①文字列からHTMLタグを除去
	$text = strip_tags( $content );
	// ②文字列の数を計測
	$length = mb_strlen( $text );
	// ③日本人の可読文字数は1分間で約400字から600字らしいので、(出展：角川ミニッツブック 400で割って四捨五入)
	$length_per_minute = 400; 
	return round( $length / $length_per_minute );
}
```

この関数には計算したいテキストを $content で渡すことを想定しています。

①渡された文字列からHTMLタグを除去   
  
文字列には a タグなどの HTML が混ざっているので、PHP の strip_tags を使って HTML を取り除き純粋なテキストにします。
   
②文字列の数を計測  
  
純粋なテキストになったところで mb_strlen() 関数で文字数を数えます。

③分数を計算  
  
1分間で約400字が読めるとしてテキスト数を 400 で割った数を返すようにします。round()関数で小数点以下を四捨五入したものを返します。

この関数に以下のテキストを渡してみます。

$content = '親譲りの無鉄砲で小供の時から損ばかりしている。小学校に居る時分学校の二階から飛び降りて一週間ほど腰を抜かした事がある。なぜそんな無闇をしたと聞く人があるかも知れぬ。別段深い理由でもない。新築の二階から首を出していたら、同級生の一人が冗談に、いくら威張っても、そこから飛び降りる事は出来まい。弱虫やーい。と囃したからである。小使に負ぶさって帰って来た時、おやじが大きな眼をして二階ぐらいから飛び降りて腰を抜かす奴があるかと云ったから、この次は抜かさずに飛んで見せますと答えた。（青空文庫より）';

```
$minutes = count_reading_minutes( $content );
var_dump( $minutes );
↓
float(1)
```
1分で読めるという計算結果！

##計算した分数と定型文「この記事は○分で読めます」を合わせて表示する関数を作る

```
function reading_minutes_shortcode( $attr, $content = '' ) {

	$post    = get_post(); // global $post とほぼ同じ動作。
	// ①投稿記事の情報を取得
	$content = $post->post_content;
	// ②投稿記事情報から本文をとりだす
	$minutes = count_reading_minutes( $content );
	// ③先につくった関数へ本文を渡す
	
	$template = 'この記事は約%d分で読めます。';
	$text     = sprintf( $template, $minutes );
	// ④定型文を作り分数を入れる

	//エスケープ大切！
	return '<span class="reading-minutes">' . esc_html( $text ) . '</span>';
	// ⑤「この記事は○分で読めます」を返す
}
```

①投稿記事の情報を取得  
  
get_post() 関数でその投稿記事のIDやタイトル、パーマリンクなど総合的な情報を取得します。

②投稿記事情報から本文をとりだす  
    
総合的な記事情報から今回必要な本文部分を取り出します。

③先につくった関数へ本文を渡す 
   
本文部分をテキストが何分で読めるかを計算する関数 count_reading_minutes() へ渡します。

④定型文を作り分数を入れる  
  
PHP の sprintf() 関数を使って定型文と分数を合体させます。
%d と書いた部分に sprintf() の第二引数で指定したものを整数として扱って表示します。

sprintf の詳細は PHP マニュアルでご確認ください。  
[PHP: sprintf - Manual](http://php.net/manual/ja/function.sprintf.php)

⑤「この記事は○分で読めます」を返す 
  
前項④で作った「この記事は○分で読めます」を esc_html() 関数を使ってエスケープしデータを無害化して返します。  
[データ検証 - WordPress Codex 日本語版](http://wpdocs.osdn.jp/%E3%83%87%E3%83%BC%E3%82%BF%E6%A4%9C%E8%A8%BC)
   
   
##ショートコード化する

ここまでに作った reading_minutes_shortcode() 関数をショートコードに登録して投稿記事内で呼び出せるようにします。 

```
add_shortcode( 'reading-minutes', 'reading_minutes_shortcode' );
```

これで['reading-minutes']というショートコードが完成！！  
実際に投稿記事へ['reading-minutes']と記述してみましょう。
  

***
  

#フィルターフックを使って「この記事は約○分で読めます。」を自動で表示してみよう

ショートコードを投稿記事にいちいち入力するのめんどくさい！  
↓  
フィルターフックを使って投稿記事本文の前に自動的に表示するようにしてみよう！

##フィルターフックとは

WordPress 本体やプラグインの動作・表示などをコアファイル（元のファイル）を改変することなくカスタマイズできる仕組みのことです。  
  
WordPress 本来の動作を変更したい時に、コアファイルを改変してしまうと予期せぬ不具合が起きたり、WordPress のアップデートの際にカスタマイズした部分が消えてしまったりします。  
そのようなことがおきないよう、WordPress には安全にカスタマイズするための仕組みが用意されています。  

今から行おうとしている投稿記事の前にテキストを表示したいというようなカスタマイズの場合、  
投稿記事本文に対して処理するための「the_content」というフィルターフックが用意されています。   
「the_content」に対してこんな処理を追加したいという記述をすれば、その処理を行った本文が表示されるようになります。
  
あらかじめ、WordPress 側で適応するようになっているフィルターフックもあります。  
例えば、投稿記事のテキスト等などは自動的に p タグで囲まれてしまいます。これもフィルターフックの機能を使って動作しています。      
p タグで囲む動作をなくしたい場合、この機能を「the_content」に適応しないようにする記述をすると、p タグが入らないようになります。    
    
このようにフックではさまざまな機能を、機能ごとに分けて付けたり外したりできるようになっています。 
   
[プラグイン API/フィルターフック一覧 - WordPress Codex 日本語版](http://wpdocs.osdn.jp/%E3%83%97%E3%83%A9%E3%82%B0%E3%82%A4%E3%83%B3_API/%E3%83%95%E3%82%A3%E3%83%AB%E3%82%BF%E3%83%BC%E3%83%95%E3%83%83%E3%82%AF%E4%B8%80%E8%A6%A7) 
  
  
##フィルターフックの使い方

何か処理を追加したい場合は、以下のように add_filter() 関数を使用して、機能を追加したいフィルターフックと機能を記述します。  
これはプラグインファイルや functions.php へ記入します。
  

```
add_filter('フィルターフック名', '行いたい処理の関数名');
function 行いたい処理の関数名(){
	〜行いたい処理〜
}
```

処理を外したい場合は remove_filter() 関数を使用して、フィルターフック名と外したい機能を記述します。

```
remove_filter ('フィルターフック名', '外したい処理の関数名');
```

ちなみに、p タグを自動的につける機能は wpautop という名前になっているので、以下のように記述すると外すことができます。  
  
```
remove_filter ('the_content', 'wpautop');
```
  
##フィルターフックで「この記事は約○分で読めます」を表示する機能を追加する

まず、追加したい機能の関数を作ります。

```
function add_reading_minutes_to_the_content( $content ) {
	$minutes              = count_reading_minutes( $content );
	$template             = 'この記事は約%d分で読めます。';
	$text                 = sprintf( $template, $minutes );
	$reading_minutes_html = '<span class="reading-minutes">' . esc_html( $text ) . '</span>';

	return $reading_minutes_html . $content;
}
```

表示する機能自体はショートコードで作った関数とほぼ同じです。  
引数にはフィルターフックで呼び出された時に本文の情報が渡されるので、それをショートコードを作った時の関数 count_reading_minutes() へ渡し何分で読めるかを算出します。  
それを定型文と合わせて表示する仕組みです。  

ショートコードの時とちがうのは、最後に処理された値を返す時に本文の情報も一緒に返すところです。
ここで一緒に本文の情報を付けないと、本文が空になってしまいます。

そして、この関数を add_filter() 関数を使って、記事本文をカスタマイズするための the_content フィルターフックと結びつけまず。

```
add_filter( 'the_content', 'add_reading_minutes_to_the_content' );
```

  
***
  
#アクションフックを使って「この記事は約○分で読めます」にスタイルをあててみよう

「この記事は約○分で読めます」の HTML へ CSS でスタイルをあててデザインを変えてみましょう！  
スタイルシートの追加をアクションフックを使って行います。
 
  
##アクションフックとは

アクションフックもフィルターフックと同じくコアファイルを改変せずにカスタマイズできる仕組みの1つですが、  
アクションフックは WordPress の何かのイベント（ヘッダーを表示する時や投稿を保存する時など）に応じて処理を差し込んだり、  
追加されている処理するを外すことができます。
     
[プラグイン API/アクションフック一覧 - WordPress Codex 日本語版](http://wpdocs.osdn.jp/%E3%83%97%E3%83%A9%E3%82%B0%E3%82%A4%E3%83%B3_API/%E3%82%A2%E3%82%AF%E3%82%B7%E3%83%A7%E3%83%B3%E3%83%95%E3%83%83%E3%82%AF%E4%B8%80%E8%A6%A7)
    
##アクションフックの使い方
  
使い方もフィルターフックと似ています。  
処理を追加したい場合は add_action() 関数を使って、追加したい処理が書かれた関数を登録します。  
   

```
add_action('アクションフック名', '行いたい処理の関数名');
function 行いたい処理の関数名(){
	〜行いたい処理〜
}
```  

処理を外したい場合は remove_action() 関数を使用して、アクションフック名と外したい機能を記述します。

```
remove_filter ('アクションフック名', '外したい処理の関数名');
```

##「この記事は約○分で読めます」にあてるスタイルシートをアクションフックで読み込む
  
スタイルシートを読み込む関数を作ります。

```
function add_reading_minutes_styles() {
	wp_enqueue_style( 'plugin-hans-on-sample', plugin_dir_url( __FILE__ ) . 'style.css' );
}
```

wp_enqueue_style() 関数を使ってプラグインフォルダに置いた style.css ファイルを登録します。
  
そして、この関数を wp_enqueue_scripts アクションフックで呼び出すように登録します。  
※wp_enqueue_scripts は登録されているスクリプトを読み込むタイミングで実行されるアクションフックです。

```
add_action( 'wp_enqueue_scripts', 'add_reading_minutes_styles' );
```

好きなスタイルを書いてデザインを変更しましょう！　　
