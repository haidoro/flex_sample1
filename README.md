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


## main内のsectionでのポイント

section要素内のh2のテキストがグラデーションになっています。
これは次のコードによるものです。「background-clip」をうまく活用した方法で実現しています。テキストの文字色を透明（transparent）にすることがポイントです。

```
 background-image: linear-gradient(to right, rgba(0, 225, 255, 0.8), rgba(38, 0, 255, 0.8));
 -webkit-background-clip: text;
 background-clip: text;
 color: transparent;
```

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






