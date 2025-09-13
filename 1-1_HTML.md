---
tags:
  - HTML&CSS
---

# 操作と特殊文字

保存　 Ctrl + S
リロード　 Ctrl + R

## 特殊文字

```
<	&lt;
>	&gt;
"	&quot;
&	&amp;
©	&copy;
¥	&yen;
```

強調タグ　`<em>`　　　　　　重要性タグ　`<strong>`

<br/><br/>

# リスト

## 順不同型リスト

```
<ul>
  <li>順不同型のリストを表しています</li>
  <li>順不同型のリストを表しています</li>
  <li>順不同型のリストを表しています</li>
</ul>
```

## 番号順リスト

```
<ol>
  <li>番号順のリストを表しています</li>
  <li>番号順のリストを表しています</li>
  <li>番号順のリストを表しています</li>
</ol>
```

## 説明リスト

```
<dl>
  <dt>用語</dt>
  <dd>説明文</dd>
  <dt>用語</dt>
  <dd>説明文</dd>
</dl>
```

<br/><br/>

# リンク

- リンクタグ　　`<a href="リンク先のパス">アンカーテキスト</a>`
- 別タブで開く場合　　`<a href="リンク先のパス" target="_blank">アンカーテキスト</a>`

<br/>

## パスの種類

### 絶対パス　 URL

### 相対パス　ファイルの場所

- 同じ階層へのパス　`<a href="ファイル名">同じ階層のファイルへ</a>`
- 下の階層へのパス　`<a href="同じ階層のフォルダ名/下の階層のファイル名">下の階層のファイルへ</a>`
- 上の階層へのパス　`<a href="../上の階層のファイル名">上の階層のファイルへ</a>`

<br><br>

# 画像と動画

## 画像タグ

`<img src="表示したい画像ファイルまでのパス" alt="代替テキスト">`

## 動画タグ

`<video src="動画までのパス" 再生方法></video>`

### 再生方法

- コンパネで再生　 controls
- 自動再生　 autoplay muted (Chrome や Satari では muted 属性で音を出ないように設定する)
- 繰り返し再生　 loop autoplay muted

# 表作成手順

1. 全体を table タグで囲む
2. tr タグで各行をマークアップ
3. th タグで見出しの各セルをマークアップ
4. 見出しに対する内容のセルを td タグでマークアップ  
   ※表に境界線をつける　`<table border="1">`

### セルの水平結合

`<th colspan="結合する数">`

### セルの垂直結合

`<th rowspan="3">`

<br><br>

# フォームタグ

## form タグ

`<form action="送信先" method="送信方法">
・・・ フォームのコントロール部品 ・・・</form>`

### 送信方法

- get 検索キーワード用（初期値）
- post 　個人情報用

<br>

## form タグ子要素

### input タグ（一行入力 or 選択部品）

`<input type="部品の種類" name="部品の名前" 
value="初期値 / 送信されるデータ / ボタンのラベル"/>`

#### 部品の種類

- text 1 行分のテキストフィールド
- tel 1 行分の電話番号フィールド
- url 1 行分の URL フィールド
- email 1 行分のメールアドレスフィールド
- search 1 行分の検索フィールド
- date 年月日入力用部品
- password パスワード
- radio ラジオボタン（複数選択できない選択部品）
- checkbox チェックボックス（複数選択できる選択部品）
- file ファイル送信用ボタン
- submit 送信ボタン

#### value

- 初期値　部品の種類が入力フィールドの場合
- 送信されるデータ　部品の種類が選択部品の場合

<br>

### textarea タグ（複数行入力）

`<textarea name="部品の名前" cols="1行あたりに表示するだいたいの文字数" rows="表示する行数">初期表示文</textarea>`

表示したい場所に配置

```
<form action="送信先" method="送信方法">
  <textarea name="textarea" cols="文字数" rows="行数"></textarea>
</form>
```

<br>

### select タグ（セレクトボックス）

セレクトボックス全体を select タグで囲む
各選択肢を option タグでマークアップ

```
<select name="部品の名前">
  <option value="送信する値">選択肢１</option>
  <option value="送信する値">選択肢２</option>
  <option value="送信する値">選択肢３</option>
</select>
```

送信する値はサーバーへ送られる ID やデータ

<br>

### label タグ（入力内容を示すラベル）

label タグでラベルとなるテキストと部品の両方を囲む方法  
`<label> ラベル内容：<input type="部品" name="部品の名前" /></label>`

<br><br>

# グループ化タグ

- div  
  CSS の装飾やコンテンツを分けるためのタグ  
  レイアウトに影響を与えない

- span  
  CSS の装飾や要素をグループ化するためのタグ  
  レイアウトに影響を与えない

<br><br>

# セクション

- section 　章・節・項など
- article 　独立したセクション  
  　　　　　 section の内容を内包し、article タグでマークアップした部分だけを抜きとっても成立する必要がある。

```
<article>
  <h1>この文章のタイトル</h1>
  <section>
    <h2>第一章の見出し</h2>
    <section>
      <h3>節の小見出し</h3>
      <p>文章の内容。文章の内容。文章の内容。</p>
      <p>文章の内容。文章の内容。文章の内容。</p>
    </section>
    <section>
      <h3>節の小見出し</h3>
      <p>文章の内容。文章の内容。文章の内容。</p>
      <p>文章の内容。文章の内容。文章の内容。</p>
    </section>
  </section>
  <section>
    <h2>第二章の見出し</h2>
    <section>
      <h3>節の小見出し</h3>
      <p>文章の内容。文章の内容。文章の内容。</p>
      <p>文章の内容。文章の内容。文章の内容。</p>
    </section>
    <section>
      <h3>節の小見出し</h3>
      <p>文章の内容。文章の内容。文章の内容。</p>
      <p>文章の内容。文章の内容。文章の内容。</p>
    </section>
  </section>
</article>
```

<br><br>

# ページレイアウト

<br>

```
<header>全てのページの先頭に一貫して表示されるロゴやナビゲーションのグループや、セクションの導入部分など。
  <nav>
    <h1>タイトル</h1>
    <ul>
      <li><a href="#">主要ナビ</a></li>
      <li><a href="#">主要ナビ</a></li>
      <li><a href="#">主要ナビ</a></li>
    </ul>
  </nav>
</header>
<main>
  <h2>ページの固有コンテンツ</h2>
  <article>
    <h3>記事タイトル</h3>
    <p>記事内容</p>
  </article>
  <article>
    <h3>記事タイトル</h3>
    <p>記事内容</p>
  </article>
</main>
<aside>メインコンテンツから切り離し可能な補足コンテンツ
  <div>サイドバー</div>
</aside>
<footer>全てのページの最後に一貫して表示される連絡先情報や、著作権情報、関連リンクなど。
  <ul>
    <li>連絡先</li>
    <li>著作権</li>
    <li>リンク</li>
  </ul>
</footer>
```

<br><br>

# 著作権表示

`<small>&copy; 帰属者名</small>`

<br><br>

# メタデータタグ

そのファイルの設定や関連する情報。head タグ内に記述。

## title

検索結果やタブバーに表示されるタイトル

## link

スタイルシートやファビコン、スマホアイコンの指定  
`<link rel="リンクタイプ" href="リンク先のパス" />`  
<br>
リンクタイプ　　 stylesheet、icon、apple-touch-icon など  
<br>
スマホのホーム画面に追加されたときのアイコンはサイズ指定する  
 `<link rel="apple-touch-icon" sizes="180x180"
    href="リンク先のパス"/>`

<br><br>

# OGP 設定

SNS で URL をシェアした際に表示されるタイトルや画像、概要説明文。
head タグ内にメタタグを記述することで設計。

```
<head prefix="og: http://ogp.me/ns# fb: http://ogp.me/ns/ fb# prefix属性: http://ogp.me/ns/ prefix属性#">
  <meta property="og:title" content=" ページのタイトル" />
  <meta property="og:url" content=" ページのURL" />
  <meta property="og:type" content=" ページの種類" />
  <meta property="og:description" content=" ページの説明" />
  <meta property="og:image" content=" サムネ画像のURL" />
</head>
```

<br>

`<head prefix="og: http://ogp.me/ns#">`  
OGP を使用することを宣言する設定 prefix 属性は２種類ある

- website トップページの場合
- article 　記事ページの場合

<br>

`<meta property="og:type" content=" ページの種類" />`  
website or article

## twitter 用の OGP 設定

`<meta name="twitter:card" content="カードの種類">`
`<meta name="twitter:site" content="@Twitter_ID">`

### カードの種類

- "summary"：小さなサムネイルとタイトル・説明（一般的）
- "summary_large_image"：大きな画像付き（よく目立つ）
- "app"：アプリリンク用（特殊用途）
- "player"：動画や音声プレイヤー用

<br>
※OGPはページごとに設定する必要がある

<br><br>

# script タグ

`<script src="js/main.js"></script>`  
JavaScript などのスクリプトの埋め込みや読み込みをする際記述。
body タグの閉じタグ直前に書くことが多いが、head タグ内に記述することもある。

<br><br>

# description タグ

```
<head>
  <meta name="description" content="スニペット"/>
</head>
```

スニペット=ブラウザ検索時に表示される説明文

<br><br>

# Emmet

### ネスト

div>a  
`<div><a href=""></a></div>`

<br>

### 繰り返しでネスト

div\*3>a

```
<div><a href=""></a></div>`
<div><a href=""></a></div>
<div><a href=""></a></div>
```

<br>

### 兄弟要素

h1+p

```
<h1></h1>
<p></p>
```

<br>

### id のついた要素

div#id 名  
`<div id="id名"></div>`

<br>

### class のついた要素

div.クラス名  
`<div class="クラス名"></div>`

<br>

### 連番のついた要素

div.item-$\*3

```
<div class="item-1"></div>
<div class="item-2"></div>
<div class="item-3"></div>
```

<br>

### 属性のついた要素

input[type="number"]  
`<input type="number">`

### テキストの入った要素

a{リンク}  
`<a href="">リンク</a>`
