---
tags:
  - HTML&CSS
---

# リセット CSS

ブラウザごとにデザインが変わらないようにするための CSS<br><br>
`<link rel="stylesheet" href="css/reset.css" />` <br><br>
リセット CSS を読み込むためのパス。必ず style.css の上に記述する。

- id 属性　同じ web ページで同じ id 属性を重複させられない
- class 属性　何度でも使える

```
セレクタ{
    プロパティ:値
}
```

<br><br>

# CSS 設計ルール

- 要素名はリセット CSS や共通の CSS を記述する以外は使用しない
- id は装飾の為に使用しないほうが良い
- スタイルを与える際のセレクタは class に統一する

# CSS 命名規則

## BEM 　規則的な class の命名法

block\_\_element--modifier<br>

- block 　塊　ヘッダー、フッターサイドバーなど
- Element 　要素　 Block を構成する要素　ヘッダーの中のロゴなど
- Modifier 　装飾　複数の共通クラスの中で一部だけ装飾が異なる場合

# カスケーディング

指定が複数ある時、決められたルールに従って優先度が高い指定が１つだけ有効になる仕組み<br>
基本的には最後に記述されたものが有効

| インライン記述       | style=""      | 1000 点 |
| -------------------- | ------------- | ------- |
| id セレクタ          | #sample       | 100 点  |
| class セレクタ       | .sampl        | 10 点   |
| 属性セレクタ         | p[class]      | 10 点   |
| 要素名               | p             | 1 点    |
| 擬似要素             | p:first-child | 1 点    |
| ユニバーサルセレクタ | \*            | 0 点    |

点数が大きいほど優先順位が高くなる

<br><br>

# 要素の表示形式

## ブロック要素

横幅いっぱいの領域を持ち、幅、高さ、余白を指定できる。前後に改行が入る。  
div 　 dl 　 form 　 h1~h6 　 ol 　 p 　 table 　 ul 　など

## インライン要素

文章の一部。前後に改行は入らない。幅、高さ、余白の指定は出来ない。  
a 　 em 　 img 　 input 　 label 　 select
small 　 span 　 strong 　 textarea 　など

## インラインブロック要素

並びはインライン要素。中身はブロック要素。

<br><br>

# 文字装飾

## 文字色

- カラーネーム
- １６進数カラーコード
- RGB・RGBA カラーモデル　（透過の指定が可能）

`color:rgba(255,0,0,0.5);`  
透過の値は 0 ～ 1

<br>

## 文字の大きさ

- 絶対値指定　　`font-size: 16px;`
- 親要素に対する相対値指定　　`font-size: 50%;` または`<font-size: 0.5em;`  
  1em=100%
- HTML(32px)に対する相対値指定　　`font-size: 2rem;`

<br>

## 文字の太さ

`font-weight: 値;`

- bold
- bolder
- lighter
- 100〜900（初期値は 400）

<br>

## 文字を斜めにする

`font-style: 値;`  
italic もしくは oblique

<br>

## 文字に線をつける

`text-decoration: 値;`

- none 　なし
- underline 　下線
- overline 　上線
- line-through 　中心線

<br>

## フォントの指定

`font-family: 英数字のフォント, 日本語のフォント, 総称フォント;`

総称フォントはそれ以前に指定したフォントがブラウザと合わなかった時に適用される。  
２単語に分かれているフォント名は"(ダブルクォーテーション)で囲む

<br>

## Web フォント

- どの OS やブラウザでも共通のフォントで表示できる。
- デザイン性が高い
- ファイルサイズが大きいと読み込みが遅くなる（特に日本語）

<br>

## 文字揃え

`text-align: 値;`

- left（初期値）
- center 　中央
- right 　右揃え
- justify 　均等揃え

<br>

注意点

- 中央寄せしたい要素がインライン要素であること
- セレクタはブロック要素である親要素を指定する

```
<p>
  <span>中央揃え</span>
</p>
```

```
p {
  text-align: center;
}
```

<br>

## 行の高さ指定

`line-height: 値;`  
px 　%　単位なし

<br><br>

# 背景色指定

`background-color: 値;`

## グラデーション指定

`background: linear-gradient( 値, 値);`  
上から下へグラデーションする

- deg で角度を変える
  `background: linear-gradient(90deg, 値, 値);`  
  90 は左　-90 は右

- 円形にグラデーションする
  `background: radial-gradient( 値, 値);`

<br><br>

# 背景画像

`background-image: url("画像へのパス");`

## 背景画像のサイズ

`background-size: 値;`

- auto（初期値）
- cover 　背景領域を完全に覆う最小サイズになる
- contain 　背景領域に収まる最大サイズになる
- px 　横と高さ
- %　横と高さ

<br>

## 背景画像の繰り返し

`background-repeat: 値;`

- repeat（デフォルト）
- repeat-x 　横並びだけリピート
- repeat-y 　縦並びだけリピート
- no-repea 　一個だけ表示

<br>

## 背景画像の位置

`background-position: 値;`  
通常は左上から表示される

- キーワード（top・right・bottom・left）
- %
- px

<br>

## ショートハンド（まとめて指定）

`background : 色 url(ファイル名) 繰り返し 位置 / サイズ ;`

<br><br>

# 横幅と高さ

## 横幅

`width: auto px % vw;`

- %は親要素に対する割合
- vw は画面幅に対する割合　 0~100

<br>

## 高さ

`height: auto px % vh;`

- %は親要素に対する割合
- vh はブラウザの高さに対する相対値 0~100  
  100 は表示中のブラウザサイズいっぱいになる

<br><br>

# 余白

- content テキストや画像が表示される領域
- padding 要素の内側の余白
- border 枠線
- margin 要素の外側の余白

<br>

`box-sizing:content-box`(初期値)  
border と padding を要素の幅と高さに含めない。

`box-sizing:border-box`  
border と padding のを要素の幅と高さに含める。

<br>

中央揃え  
`margin: 0px auto;`

<br><br>

# 枠線の装飾

- border-style 　　スタイル
- border-width 　　太さ
- border-color 　　色

<br>

## 枠線のスタイル

- solid（1 本線）
- double（2 本線）
- dashed（破線）
- dotted（点線）

<br>

## 角丸

`border-radius: px or % ;`  
50%に指定すると正円になる。

<br><br>

# 影

`box-shadow: 左右の向き　上下の向き ぼかし距離　色;`

`text-shadow: 左右の向き　上下の向き ぼかし距離　色;`

<br><br>

# Flexbox

`display:flex`

横並びにしたい要素の親要素の display プロパティに flex を指定することで子要素が横並びになる。

## 並ぶ向き

`flex-direction:並ぶ向き;`

- row 　左から右
- row-reverse 　右から左
- column 　上から下
- colimn-reverse 　下から上

<br>

## 水平方向の配置

`justify-content:配置;`

- flex-start 　左揃え
- flex-end 　右揃え
- center 　中央揃え
- space-between 　最初と最後を両端、間の子要素を均等に配置。
- space-content 　すべての子要素を均等に配置

### sapce-between

主に左にロゴ、右にナビゲーションを配置する際、div タグを子要素としてナビゲーションバーで使用する。

```
<div class="flex">
        <div class="logo"></div>
        <div class="actions"></div>
    </div>
```

<br>

## 垂直方向の配置

`align-items:配置;`

- stretch 　親要素の高さ、またはコンテンツの一番多い子要素の高さに合わせて広げて配置。
- flex-start 　親要素の開始位置から配置
- flex-end 　親要素の終点から配置
- center 　中央揃え

### justity-content: center との組み合わせ

親要素に対して中心に配置する

```
<div class="img-inner">
            <div class="text">Commit...</div>
        </div>
```

img-inner に
`justify-content: center;`
`aligin-items: center;`  
を指定することで親要素に対して中心に配置する。

<br>

## 折り返し

`flex-warp: 値;`

- nowrap 　折り返さず、1 行で表示。
- wrap 　親要素の幅を子要素の幅が上回った際に、折り返され複数行になる。
- wrap-reverse 　下から上に折り返す。

<br>

## Flexbox と inline-block の違い

inline-block はピンポイントで各要素を横並びにでき、Flexbox は子要素を全て横並びにすることができる。

<br><br>

# Position

- static（初期値）
- relative
- absolute
- fixed
- sticky

Position プロパティを使用しなくても配置できる場合は使用しない。  
margin > position

<br>

## 相対位置(relative)

元々 static（初期値）だった場所から位置を指定する。

`<div class="relative red-box"></div>`

```
.relative {
  position: relative;
  top: 50px;
  left: 50px;
}
```

<br>

## 絶対位置(absolute)

親要素が static 以外に指定されている場合、その親要素を基準に位置を指定する。  
※子要素に対して指定する。

```
<div class="relative red-box">
  <div class="absolute blue-box"></div>
</div>
```

```
.relative {
  position: relative;
  top: 50px;
  left: 50px;
}

.absolute {
  position: absolute;
  top: 100px;
  left: 100px;
}
```

<br>

## 固定位置

### sticky

スクロールしても位置を固定することができる。  
ヘッダー、メニュー、カスタマーサポートなど。  
ブラウザの左上を基準にして位置を指定する。

### static

要素が画面上部に到達したときに、画面上部で固定する。  
※兄弟要素がないと機能しない。

<br>

## 要素の重なり順(z-index)

同じ位置に要素が重なっている時、どの要素を前から表示するかを指定する。  
`z-index: 整数値;`  
指定した整数値が大きいほど前に表示される。

<br><br>

# Float

指定した要素に他の要素を回り込ませる。
画像に横に文字が回り込んでくる配置などに利用する。

- none（初期値）
- left
- right

## Clear

他が回り込むことにより隠れた要素に対し、回り込みを解除する。

```
.item1 {
  background: #ff0000;
  width: 150px;
  height: 150px;
  float: left;
}
.item2 {
  background: #00ff00;
  width: 150px;
  height: 150px;
  float: left;
}
.item3 {
  background: #0000ff;
  width: 150px;
  height: 150px;
  clear: both;
}
```

<br><br>

# CSS Grid Layout (2 次元レイアウト)

1. グリッドの定義  
   親要素にレイアウトを適用  
   `display: grid;`  
   <br>
2. 横ライン（行）の分割  
   行の分割数と各行の高さを指定
   `grid-template-rows: 値;`  
   <br>
3. 縦のライン（列）の分割  
   列の分割数と各列の高さを指定
   `grid-template-columns: 値;`  
   <br>
   例  
   `grid-template-columns: 2fr 1fr 1fr;`  
   fr=余り部分の数値、または分割した際の割合。  
   例の場合、max=4ft、2ft=４分割中の２。  
   <br>
4. アイテムを配置
   子要素に配置を適用

```
grid-column: 1/4;
grid-row: 1/2;
```

値は何分割目～何分割目までを占めるかで記載。

<br><br>

# 要素の変形

## 回転

回転させたい要素をセレクタにして記載  
`transform: rotate(回転角度deg);`  
deg は時計回り　　-deg は半時計回り

<br>

## 移動

`transform: translate(X軸値,Y軸値);`  
値には絶対値、相対値どちらも指定できる。

- 値がプラス　　 X 軸右方向、Y 軸下方向
- 値がマイナス　　 X 軸左方向、Y 軸上方向

<br>

## 上下左右中央配置

上下左右中央に揃える際、translate を position とセットで利用する。

```
親要素{
  position: relative;
  background: black;
  color: white;
  width: 300px;
  height: 300px;
}
子要素{
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
```

<br><br>

# 擬似要素

## before

選択した要素の直前に擬似要素を作成する。  
content プロパティを使用して、装飾的な内容追加するために用いられることが多い。

```
p::before {
  content: "COACH";
  color: #ff0000;
}
```

<br>

## after

選択した要素の直後に擬似要素を作成する。  
作成位置以外は before と同じ。

## 擬似要素の使い方

## 擬似要素を使う意味

擬似要素は検索エンジンに認識されないため、SEO を気にせずに  
コンテンツを配置することができる。

<br>

## 見出しアイコンに使用する

content の中を空にして background で画像を出力する

```
要素::before {
  content: "";
  background: url (画像パス);
  background-size: cover;
  vertical-align: middle;
  width: 40px;
  height: 40px;
  display: inline-block;
}
```

<br>

## ボタンにアイコンを表示

元の要素に position プロパティを使用して配置する

```
要素 {
  position: relative;
  background-color: #55bbbb;
  width: 200px;
  display: block;
  padding: 18px;
  text-align: center;
  color: #fff;
  font-size: 16px;
  font-weight: bold;
  text-decoration: none;
  border-radius: 30px;
  margin: 100px auto;
}

要素::after {
  content: "";
  position: absolute;
  top: 50%;
  right: 15px;
  background: url(../img/download__icon.svg) no-repeat;
  background-size: cover;
  width: 30px;
  height: 30px;
  transform: translate(0, -50%);
}
```

## 吹き出しアイコンに使用する

```
<div class="speech__bubble">
  <p>サンプルテキスト</p>
</div>
```

```
.speech__bubble {
  padding: 10px 20px;
  background: red;
  position: relative;
  display: inline-block;
  color: #fff;
}


.speech__bubble::after {
  position: absolute;
  top: 100%;
  left: 50%;
  content: "";
  margin-left: -10px;
  border: 10px solid transparent;
  border-top: 10px solid red;
}
```
