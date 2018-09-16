# Flexレイアウトの練習  
今回のページを作成するポイントとしてfont-sizeを筆頭に、marginやpaddingあるいはwidthやheightなど全ての単位をpxではなく「rem」 を使用しています。  
「rem」を単位として使用する場合はhtmlセレクタに対して基準となる数値を決めます。 
通常は10pxにしておくとそれぞれの要素での値を決めやすくなります。ただし、基準の数値においても固定の数値を使用するとユーザーの設定次第ではレイアウトに影響が出ます。
したがってhtmlのfont-sizeは62.5％という記述にします。

##header  
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

## main内のsectionでのポイント

section要素内のh2のテキストがグラデーションになっています。
これは次のコードによるものです。「background-clip」をうまく活用した方法で実現しています。テキストの文字色を透明（transparent）にすることがポイントです。

```
 background-image: linear-gradient(to right, rgba(0, 225, 255, 0.8), rgba(38, 0, 255, 0.8));
 -webkit-background-clip: text;
 background-clip: text;
 color: transparent;
```






