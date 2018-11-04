# Flexレイアウトの練習  
今回のページを作成するポイントとしてfont-sizeを筆頭に、marginやpaddingあるいはwidthやheightなど全ての単位をpxではなく「rem」 を使用しています。  
「rem」を単位として使用する場合はhtmlセレクタに対して基準となる数値を決めます。 
通常は10pxにしておくとそれぞれの要素での値を決めやすくなります。ただし、基準の数値においても固定の数値を使用するとユーザーの設定次第ではレイアウトに影響が出ます。
したがってhtmlのfont-sizeは62.5％という記述にします。

## header  
header部分のHTMLコードはシンプルなものです。
h1要素の領域とボタン領域の2つの領域を作ります。

### header領域のCSSの記述ポイント

h1要素の領域とボタン領域の2つの領域は.headerに対して「display:flex」と「flex-direction: column」で垂直方向にレイアウトします。
flexレイアウトの特徴である垂直方向の整列が簡単にできます。今回は「justify-content: center」と「align-items: center」で水平方向と垂直方向の中央揃えを行なっています。
またロゴについては「position: absolute」で配置します。「position: absolute」はflexアイテムから外れますので、「display:flex」の影響を受けません。

background-imageは複数の画像を指定できます。その機能をうまく活用して、背景画像の上にグラデーションを重ねています。グラデーションはbackground-imageで設定することを思い出して下さい。そしてグラデーションの指定にrgba()を使うことで透明度をつけます。これで画像にグラデーションを掛け合わせることができます。

背景画像下部が斜めに切られているのはPhotoshopで加工した訳ではありません。
「.header」に対して`clip-path: polygon(0 0, 100% 0, 100% 80%, 0 100%)`を設定しています。「clip-path」プロパティはIEでは表示されませんので注意して下さい。  
[CSS clip-path maker](https://bennettfeely.com/clippy/)ページで簡単に座標を作れますので活用すると良いでしょう。  

キーフレームアニメーションの例がh1の動きで、transitionアニメーションの例がボタンの挙動です。ボタンの挙動は影の付け方がポイントです。  
簡単な内容ですから、コードを参考にして下さい。  
尚、「backface-visibility: hidden」はアニメーションの際に変な震えを止めるためのテクニックです。

## floatによるグリッドの作成
「.row」を親要素にして、それぞれのグリッドは「.col-N-of-N」というクラス名にします。
N部分には整数が入ります。それぞれの「.col-N-of-N」グリッドには最後のグリッド以外に「margin-right」をつけます。セレクタの記述の方法は以下

```
.row [class^="col-"]:not(:last-child) {
      margin-right: 6rem; }
```

それぞれのカラムの幅の計算方法は「calc()」関数を使います。  
「calc()」は違う単位間で計算が可能です。今回は親要素いっぱいに広がるサイズを100％として、それぞれのグリッド同士のマージンを100％から差し引き、あとは必要なカラム数で割り算をして算出しています。
3等分した後に2つのグリッドを合体した場合の計算は少し複雑で次のようにします。

```
calc(2 * ((100% - 2 * 6rem) / 3) + 6rem)
//次の計算は違いますので注意。
calc(2 * ((100% - 1 * 6rem) / 3))
```

この方法だと親要素のサイズが変更されても柔軟にサイズ変更が行われますのでリキッドタイプのレイアウトでは好都合です。
また算出方法が面倒ですから、この内容はSASSで記述してコンポーネントとしていつでも活用できるようにしておくと良いでしょう。

## flexレイアウトでグリッドを作る方法

floatではなくflexを使っても同様なことが当然できます。
flexへの変更は簡単です。
「.row 」のプロパティに「display: flex」を追加するだけです。
あとは不要なfloatとclearfixを削除するだけです。
flexアイテムのflexプロパティに初期値は「flex:0 1 auto」ですから現在の挙動に合っていますので特にflexプロパティの操作も必要ありません。
＊「flex:0 1 auto」だと親要素が拡大してもflexアイテムは拡大せず、縮小した場合はそれにしたがって均等に縮小します。またwidthの値はautoですから個別に指定したwidthの値に従います。

グリッドのレイアウトはあらかじめ2カラム、3カラム、4カラムのものを作成してそれぞれクラス名で対応させます。

```
 .col-1-of-2{
      width:calc((100% - #{$gutter-horizontal}) / 2);
  }
  .col-1-of-3 {
    width:calc((100% - 2 * #{$gutter-horizontal}) / 3);
  }
  .col-2-of-3 {
    width:calc(2 * ((100% - 2 * #{$gutter-horizontal}) / 3) + #{$gutter-horizontal});
 }
  .col-1-of-4 {
    width:calc((100% - 3 * #{$gutter-horizontal}) / 4);
  }
  .col-2-of-4 {
    width:calc(2 * ((100% - 3 * #{$gutter-horizontal}) / 4) + #{$gutter-horizontal});
  }
  .col-3-of-4 {
    width:calc(3 * ((100% - 3 * #{$gutter-horizontal}) / 4) + 2 * #{$gutter-horizontal});
  }
```

また、グリッドの間隔はgutterとして設定しておきます。

```
[class^="col-"]{
    &:not(:last-child) {
        margin-right: $gutter-horizontal;
    }

  }
```

## main内のsectionでのポイント

section要素内のh2のテキストがグラデーションになっています。
これは次のコードによるものです。「background-clip」をうまく活用した方法で実現しています。テキストの文字色を透明（transparent）にすることがポイントです。

```
 background-image: linear-gradient(to right, rgba(0, 225, 255, 0.8), rgba(38, 0, 255, 0.8));
 -webkit-background-clip: text;
 background-clip: text;
 color: transparent;
```

## aboutセクションの作成
背景色にグレーを設定しますが、ヘッダー部分の画像に斜めの切れ込みを入れていますのでその部分が白の背景色で違和感が出ます。画像の下の背景色を同じにすれば良いのですが、今回はabout
領域全体をマイナスマージンで引き上げます。そして同サイズのpaddingで見出しを下げています。

### img画像の重ね方

imgタグで挿入された画像の重ね方は、各画像に対して「position: absolute」をかけるだけです。絶対配置の基準を変更するために、親要素の「.composition」には「position: relative」を設定しておきます。これで3枚の画像は同じ場所で重なります。
あとは1枚づつ画像の「left」「top」の座標を変更してずらしてやるだけです。

hoverで画像が拡大するのは「transform: scale(1.05)」を使います。また画像の周りの枠は「outline:1rem solid rgba($color-primary, .5)」を使います。  
また、枠線と画像に間隔を入れるのは「outline-offset: 2rem」を使います。
画像の入れ替えは「hover」時にz-indexの値を大きくすることで実現しています。

面白いテクニックとして画像にhoverすると対象の画像が拡大するのは上記の方法で実現されますが、対象の画像以外は縮小されています。これは次のコードで行っています。  
セレクタの書き方が参考になるはずです。

```
 .composition:hover .composition__photo:not(:hover) {
    transform: scale(0.95); }
```


## features作成

4カラムのグリッドを基本に作成します。
WebアイコンはFont AwesomeをCDNで読み込んでいます。  
Webアイコンの色はベタ塗りではなくグラデーションを活用しています。文字にグラデーションをつけた方法と同様の手法をとります。

```
.feature-box__icon{
display:inline-block;
background-image:linear-gradient(to right, $color-primary-light, $color-primary-dark);
-webkit-background-clip: text;
background-clip: text;
color:transparent;}
```

背景の傾きは「.feature-box」に対して「skewY(-7deg)」で傾けました。けれどもこのままでは中のテキストも傾きます。従って、テキスト部分を逆に傾ける処置が必要です。

```
.section-features {
  padding: 20rem 0;
  background: linear-gradient(to right bottom, rgba(0, 225, 255, 0.8), rgba(38, 0, 255, 0.8)), url(../img/features.jpg) center top/cover;
  clip-path: polygon(0 0, 100% 0, 100% 80%, 0 100%);
  transform: skewY(-7deg);
  margin-top: -10rem; }
  
.section-features > * {
    transform: skewY(7deg);}
```


## ナビゲーション作成
今回の作品のナビゲーションはPC用でもハンバーガーボタンを用意しておいてボタンのON OFFでメニューの表示・非表示を行います。  
イベントの感知はJavaScriptを使わずにチェックボックスイベントを活用します。

HTMLのコーディングはbodyタグのすぐ下にナビゲーション部分を記述します。  
また、イベントの感知はチェックボックスイベントを使いますので「input type="checkbox"」とlabel要素を活用しますのでそれぞれのタグを用意します。  
つまりこのラベル部分をクリックするとイベントを感知しますので、label要素＝ボタンとなります。

ナビゲーションの本体はli要素を使います。


## ハンバーガーボタンの作成
ハンバーガーボタンは背景色を白にして、幅と高さを7remつまり70pxとします。  
位置は固定したいので「position:fixed」で固定します。

円形にするのは「border-radius: 50%」で実現できます。正方形の形なら「border-radius: 50%」でどのようなサイズでも正円を作ることができます。  
ボタンの重ね順はz-indexで決めます。

ボタンの中の3本線のうち1本は、spanタグで幅30px 高さ2pxの細長い長方形で作成しています。
残りの2本は擬似要素beforeとafterで作成し、「position: absolute」で上下に移動しています。

## ナビゲーションの挙動
.navigation__navがナビゲーションの土台になります。height:100vhで画面いっぱいに広がり固定された状態になるはずですが、widthが0の指定のため実際には画面いっぱいに広がりません。
しかもopacity:0のため透明な状態です。

しかし、チェックボックスにチェックが入ると`opacity:1;width:100%`となって画面いっぱいに広がります。この記述は次のようにします。間接セレクタを活用しているところがポイントです。

```
.navigation__checkbox:checked ~ .navigation__nav {
  opacity: 1;
  width: 100%; }
```

## card領域の作成
まずはグリッド部分はあらかじめ作成しておいた「.col-1-of-3」のレイアウトを使用します。

この領域のポイントはカードの裏表が回転することです。HTMLの構造は次のようにします。

```
<div class="card">
  <div class="card__side card__side--front">
    表                         
  </div>
  <div class="card__side card__side--back">
    裏                         
  </div>
</div>
```

「.card」を「position:relative」にしておき、「.card__side--front」と「.card__side--back」を「position: absolute」にして同じ座標に配置します。
これで一枚のカードのように見せておきます。

マウスホバーで回転する仕組みはtransform:rotateY(180deg)を使います。

[この仕組みの詳細は別サンプルを参照ください。](https://github.com/haidoro/card)

### 画像にブレンドモードをかける
背景画像に対してブレンドモードを適用することができます。  
「background-blend-mode」プロパティを使い、様々な値を設定することができます。
値には次のものが用意されています。Photoshopでの加工の手間を省けます。

```
<blend-mode> = normal | multiply | screen | overlay | darken | lighten | color-dodge | color-burn | hard-light | soft-light | difference | exclusion | hue | saturation | color | luminosity
```

画像にクリッピングマスクをかけるには「-webkit-clip-path」プロパティを使います。
これはヘッダーで説明したことと同様の方法です。

```
-webkit-clip-path:polygon(0 0, 100% 0, 100% 85%, 0 100%);
clip-path:polygon(0 0, 100% 0, 100% 85%,0 100%);
```

## story領域の設定
上下に1ずつのカラムを配置します。これは「transform:skewX(-12deg)」で傾けます。

1つのカラムには写真とテキストが配置されます。写真は円形に切り抜かれたものです。

この写真の加工は通常「border-radius」プロパティで値を50％にすると円形になります。
けれども今回は別の方法で、「shape-outside」プロパティと「clip-path」プロパティを使って円形にしています。

この面倒な方法を取っているのは、完成品で確認してみると単純に写真とテキストが左右に並んだ状態ではなく、写真の円形に対してテキストが円形に回り込んでいることです。    
このような形状にするには写真を「border-radius」で加工するとできません。
​    
まず「shape-outside」で丸いアウトラインを作成しておいて、「clip-path」プロパティを使ってマスクするとこのような形状が可能になります。  
またテキストはflexで横並びにするのではなくfloatでテキストを流し込みます。これこそfloatの出番です！

### 画像のマウスホバーによる変化
マウスホバーによる画像の変化は「transform:scale(1.1)」で拡大しています。  
また、ぼかしは「filter:blur(3px)」フィルターを使っています。

### 背景に動画の挿入
背景には動画を挿入しています。
動画挿入のためのタグは非常に簡単ですが、各社ブラウザの再生可能な動画形式がまちまちであったり、右往左往したりで非常に扱いにくいものでした。  
現在はMPEG-4 / H.264ビデオフォーマットで収束しそうですが、一般的に多く用いられるように、webm形式も再生可能にしておきます。

動画は.section-stories要素の先頭に配置します。

```
<div class="bg-video">
  <video class="bg-video__content" autoplay muted loop>
    <source src="img/Chruch.mp4" type="video/mp4">
    <source src="img/Chruch.webm" type="video/webm">
    Your brouser is not supported!
  </video>
</div>
```
mp4は正確にはMPEG-4 / H.264ビデオフォーマットのことです。

今回は動画はmp4とwebm形式のものを用意しています。

動画のブラウザ対応は「[Can I use](https://caniuse.com/)」ページで確認できます。

動画の配置は「position: absolute」を使います。また、「z-index:-1」としてコンテンツ部分の下に配置します。  
またコンテンツ部分の邪魔にならないように「opacity:.15」で透明度をつけます。

コンテンツ部分の量次第で高さが変わります。これにフィットさせる方法として「object-fit:cover」を活用しています。



## SASSの活用

これまでの記述をSASSで記述します。

フォルダはabstract, base,component,layout,pages,を作成して、それぞれ分割してSASSで記述します。main.scssがメインとなり@importでパーシャル化したそれぞれのファイルをインポートします。

1. abstractフォルダには変数や関数あるいはmixinファイルを含めます。

2. baseフォルダはanimationファイル、baseファイル、typographyファイル、utilitiesファイルを含めます。
3. componentsフォルダはbg-videoファイル、buttonファイル、cardファイル、compositionファイル、feature-boxファイル、storyファイルを含めます。
4. layoutフォルダはfooterファイル、headerファイル、gridファイル、navigationファイルが含まれます。
5. pagesフォルダにはhomeファイルが含まれます。

## メディアクエリの活用

今回のサイトはデスクトップファーストです。

そのため、メディアクエリの外が基本デスクトップになります。

### mixinの活用

mixinを活用することで効率的にメディアクエリを記述することができます。

_mixinファイルを別途作成して、ここにmixinを記述していきます。

カスタマイズしやすいようにブレイクポイントをコメントで記述しておくとよいでしょう。

```css
/*
0-600px: phone
601-900px: tab-port
901-1200px: tab-land
1201-1800px: normal style
1801px- : big-desktop
*/
```



HTMLセレクタにfont-size:62.5%としてページ全体のフォントサイズや各種単位をrem指定した場合、この値を変更することで全体の数値の値を一括で変更できます。

これはデバイスごとに拡大縮小するためのベストプラクティスです。

次の例では600px以下の場合に、font-size:50%にする設定をmixnで記述したものです。



mixinの記述内容

```css
@mixin respond($breakpoint) {
    @if $breakpoint == phone {
        @media (max-width: 600px) {
            @content
        };
    }
```



セレクタへの記述内容

```css
html{
    @include respond-phone{
        font-size:50%;
    }
}
```



SASSのifディレクティブを使うともっと便利に記述できます。

**ブレイクポイントはpxやremで指定するのではなく、emで指定するのがベストです。remやpxで指定するとsafariで不具合が出ます。**

```css
@mixin respond($breakpoint) {
    @if $breakpoint == phone {
        @media only screen and (max-width: 37.5em) { @content };    //600px
    }
    @if $breakpoint == tab-port {
        @media only screen and (max-width: 56.25em) { @content };     //900px
    }
    @if $breakpoint == tab-land {
        @media only screen and (max-width: 75em) { @content };    //1200px
    }
    @if $breakpoint == big-desktop {
        @media only screen and (min-width: 112.5em) { @content };    //1800
    }
}
```

セレクタの記述

```css
html {
    font-size: 62.5%;   
    @include respond(big-desktop) {
        font-size: 75%; //1rem=12px
    }
    @include respond(tab-land) {
        font-size: 56.25%; //1rem=9px
    }
    @include respond(tab-port) {
        font-size: 50%;
    }
    @include respond(phone) {
        font-size: 50%; //1rem=8px
    }
}
```

次にbody要素のpaddingを設定

_baxe.scssに次の設定を追加

```css
@include respond(tab-port) {
        padding:0;
 }
```





.heading-primary—mainに次の設定を追加

```css
 @include respond(phone) {
            letter-spacing: 1rem;
            font-size:5rem;
            
        }
```



.heading-primary—subに次の設定を追加

```css
@include respond(phone) {
            letter-spacing: .5rem;
}
```





### グリッドの設定



.rowセレクタに次の設定を入れることで、tab-portサイズ以下でグリッド部分はシングルカラムにします。max-widthで幅を50remにしています。

```css
 @include respond(tab-port) {
      flex-direction: column;
      max-width:50rem;
  }
```



[class^="col-"]セレクタに対して次の設定をすることで幅を親要素に対して100％にします。親要素は上で使った.rowになります。

```css
 @include respond(tab-port) {
        width: 100% !important;
  }
```



通常フローさせると各gridの下マージンをつける必要が出てきました。

```
 &:not(:last-child){
    margin-bottom:$gutter-vertical;
    @include respond(tab-port) {
        margin-bottom: $gutter-vertical-small;
    }
  }
```



###  header部分の変更

横幅サイズを小さくするとheader部分の傾斜が急すぎることに違和感を感じます。これを対策するために以下のことを実施します。

```css
@include respond(phone){
      -webkit-clip-path: polygon(0 0, 100% 0, 100% 85vh, 0 100%);
      clip-path: polygon(0 0, 100% 0, 100% 85vh, 0 100%);
    }
```

ナビボタンも少し離れて見えます。.navigation__buttonの設定を微調整します。

```css
@include respond(tab-port){
            top:4rem;
            right:4rem;
}
@include respond(phone) {
            top: 3rem;
            right: 3rem;
}
```

.navigation__backgroundも同様にします。

```css
@include respond(tab-port) {
            top: 4.5rem;
            right: 4.5rem;
 }
@include respond(phone) {
            top: 3.5rem;
            right: 3.5rem;
 }
```



この段階でiOS5で検証すると右に余計な余白が出てしまいます。これはstory sampleのセクションのカラムがは大きいためです。



### footerの処理







## 高解像度対応画像

ロゴ画像の対応

footerのロゴを高解像度対応にしました。imgタグにsrcset属性を使用して2つの画像ファイルのパスを記述します。1倍はファイル名の後に1xと記述します。

2つ目のファイル指定は感まで区切って行い、2倍指定はファイル名の後に2xと記述します。

アートディレクションを行うにはsourceタグを使用して記述します。ここではメディアクエリを使用できますので、ブレイクポイントで切り分けた時の表示画像を指定します。さらに高解像度用に複数の画像を指定できます。



```css
<picture class="footer__logo">
  <source srcset="img/logo-red-1x.png 1x,img/logo-red-2x.png 2x" media="(max-width:37.5em)">
  <img srcset="img/logo-1x.png 1x,img/logo-2x.png 2x" alt="footer logo"　src="img/logo-1x.png">
</picture>
```



別の記述をtrainの写真でも行なっています。こちらは同じ画像で300pxと1000pxのサイズのものを用意しています。画面の横幅次第で表示する画像のサイズを変更します。srcsetで2枚のサイズ違いの画像を指定します。ファイル名の後にサイズを300wのように記述します。

続いてsaizes属性でメディアクエリを指定してその後に表示したいサイズをvw単位で記述しています。

src属性はこれらの記述に対応してないブラウザ向けの記述です。



```css
<img srcset="img/train1_small.jpg 300w,img/train1.jpg 1000w" sizes="(max-width:900px) 20vw,(max-width:600px) 30vw,300px" alt="" class="composition__photo composition__photo--p1" src="img/train1.jpg">
```



###live-sarver起動とSASSコンパイルのWatch稼働

開発時の開始コマンド

```
npm run start
```

### style.comp.cssファイル作成

次のコマンドを入力

```
npm run compile:sass
```

### 複数CSSファイルの結合

ファイルの結合はSASSファイルとしておいて、結合する方法もあります。
concatを活用するにはインストールが必要です。  

```
npm install concat --save-dev
```

package.jsonに記述されたいる内容はstyle.comp.cssファイルとicon-font.cssを結合するものですが、今回のサンプルではicon-font.cssを使用していませんので実行してもエラー表示になります。
icon-font.cssが存在する場合は、次のコマンドを入力

```
npm run concat:css
```

### autoprefixerの導入

autoprefixerをインストールしておく必要があります。

```
npm install autoprefixer --save-dev
```

```
npm install postcss-cli --save-dev
```

今回のサンプルはstyle.concat.cssを作成していませんので実行するとエラーになります。実行するにはstyle.comp.cssをコピー後リネームしてstyle.concat.cssを予め作成しておきます。

```
npm run prefix:css
```

### compress:css作成

style.cssとして圧縮ファイルが作成されます。

```
npm run compress:css
```

以上の4つの動作をbuildコマンドで実行できます。

予めnpm-run-allを導入しておく必要があります。

```
npm install npm-run-all --save-dev
```

4つまとめて実行は以下コマンド

```
npm run build:css
```











尚、このページのデザインは[Jonas Schmedtmann氏](http://codingheroes.io/)の作成したページを参考にして内容を書き換えたものです。