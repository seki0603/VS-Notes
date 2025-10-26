# JavaScript2

## 文字列

### 文字列へのアクセス

```
const str = "プログラミング";

console.log(str[0]); // 出力結果：プ
console.log(str[1]); // 出力結果：ロ
```

### 文字列の分解と結合

- split...文字列を分解して配列にする。
- join...配列を結合して文字列を作る。

```
const strings = "HTML・CSS・JavaScript".split(".");

console.log(strings); // 出力結果："HTML", "CSS", "JavaScript"
```

```
// 第一引数に指定した文字を配列の「,」と置き換えて結合
const str = "HTML・CSS・JavaScript".split(".").join("、");

console.log(str); // 出力結果：HTML、CSS、JavaScript
```

### 文字列の長さ

- length...文字列の要素数を返す。

```
const str = "プログラミング";

console.log(str.length); // 出力結果：7
```

### 文字列の比較

```
console.log("プログラミング" === "プログラミング"); // 出力結果：true
console.log("PHP" === "Ruby"); // 出力結果：false
```

### 文字列の一部を取得

- slice...第一引数で開始位置、第二引数で終了位置を指定し、その範囲を取り出す。

```
const str = "プログラミング";

console.log(str.slice(2, 5)); // 出力結果：グラミ
console.log(str.slice(2)); // 出力結果：グラミング
```

- substring...slice と同じ働き。

indexOf と組み合わせて使う場合。

```
const url = "https://example.com?param=1";

const indexOfQuery = url.indexOf("?");
const queryString = url.slice(indexOfQuery);

console.log(queryString); // 出力結果：?param=1
```

### 文字列の検索

- indexOf...指定した要素が文字列のどの位置にあるかを返す。該当する要素が無い場合は-1 を返す。

```
const str = "HTMLとCSSとJavaScriptとPHP";

const indexOfJS = str.indexOf("JavaScript");
console.log(index.OfJS); 出力結果：9
```

- 文字列にマッチした文字列の取得

```
const str = "JavaScript";
const searchWord = "Script";
const index = str.indexOf(searchWord);

if (index !== -1) {
    console.log(`${searchWord}が見つかりました`);
} else {
    console.log(`${searchWord}は見つかりませんでした`);
}
```

- includes...検索文字列が含まれているかを検索する。

```
const str = "HTMLとCSSとJavaScriptとPHP";

console.log(str.includes("PHP")); // 出力結果：true
console.log(str.includes("Ruby)); // 出力結果：false
```

## ダイアログ

ユーザーにメッセージを表示したい時に使う。

- alert...警告メッセージを表示するとき。
- confirm 関数...「はい」「いいえ」のダイアログを表示するとき。
- prompt...ユーザーの入力を受け付けるとき。

### alert

alert 関数の引数にメッセージ内容を指定する。

```
alert("メッセージ");
```

### confirm

confirm 関数の引数にメッセージ内容を指定する。  
返り値は boolean 型になる。

```
confirm("メッセージ");
```

### prompt

ダイアログで表示させたい文字列を引数に指定する。
戻り値は入力された文字列になる。

```
const result = prompt("入力してください");

console.log(result); // 入力欄に入れた内容が反映される
```

## スケジューリング

関数をすぐに実行せず、任意の時点で実行させるために使われる技術。

- setTimeout...指定時間経過後、一度だけ関数を実行する。
- setInterval 指定した間隔で定期的に関数を実行する。

### setTimeout

`const timerId = setTimeout(func, delay, arg);`

- func...関数名
- delay...実行前の遅延時間（ミリ秒単位：0.001 秒）
- arg...関数の引数

```
function say(phrase, who) {
    alert(phrase + ", " + who);
}

setTimeout(say, 1000, "Hello", "COACHTECH");
// 1秒後にHello, COACHTECHが表示される
```

### clearTimeout を使ったキャンセル

```
function say() {
    alert("Hello");
}

const timerId = setTimeout(say, 1000);
clearTimeout(timerId);
// say関数をスケジュールし、キャンセルしているため、結果何も起きない。
```

### setInterval

構文は setTimeout と同じ。定期的に与えられた時間間隔で実行される。  
呼び出しを止めるには clearInterval を使用する。

```
function say() {
    alert("Hello");
}
const timer = setInterval(say, 1000);
// 1秒ごとにsay関数を実行

function stop() {
    clearInterval(timer);
}
setTimeout(stop, 5000);
// 5秒後に呼び出し停止
```

## オブジェクト

複雑なデータを扱う際の特殊なデータ型

```
const user = {
    name: "太郎", // キー "name"に値 "太郎"が格納される
    age: 20,
    gender: "男性"
};
```

```
const オブジェクト = {
    キー: 値 // プロパティ
}
```

- オブジェクトは{}とプロパティの一覧から成る。
- プロパティは key:value で構成される。
- key は文字列、value 何でも構わない。

### オブジェクトへのアクセス

ドット表記を使う

```
const user = {
    name: "太郎",
    age: 20,
};

console.log(user.name);
console.log(user.age);
```

### オブジェクト内のメソッド

オブジェクト内に関数を記述できる。  
オブジェクト内の関数は「function」を省略することも出来る。

```
const user = {
    say() {
        console.log("Hello");
    },
};

user.say(); // 関数の呼び出し
```

### メソッドの中の「this」

```
const user = {
    name: "太郎",
    age: 20,
    say() {
        console.log(this.name);
    },
};

user.say();
```

- オブジェクトの中の関数からオブジェクト内の情報にアクセスできる。
- this.key でアクセス。
- アロー関数内では this は使えない。

### 分割代入

複数のプロパティを持つオブジェクトから特定のプロパティのデータのみ取り出したい時。

```
const o = {p: 42, q: true, r: "オブジェクト"};
const {p, q} = o; // oからpとqの値を取り出して代入

console.log(p); // 出力結果：42
console.log(q); // 出力結果：true
```

### Math オブジェクト

主に数値計算を行う際に利用する。

- Math.random()...配列内のランダムな数値を取り出す。少数による乱数を生成できる。
- Math.ceil()...()内に引数を与えることができ、引数の値を整数にできる。（少数を繰り上げることで整数にする）
- Math.floor()...引数の値を整数にできる。（少数を繰り下げることで整数にする）
- Math.round()...少数を四捨五入することで整数にする。

```
console.log(Math.random()); // 出力結果：0~1.0未満の乱数
console.log(Math.ceil(5.4)); // 6
console.log(Math.floor(5.4)); // 5
console.log(Math.round(5.4)); // 5

console.log(Math.floor(Math.random() * 20));
// 出力結果：0~19のうちの整数の乱数
```

### JSON

- JSON...JavaScript Object Notation
- バックエンドにデータを送るときは、オブジェクトを JSON に変換して送る。
- JSON は構造化したデータを表すためのデータの記述言語
- JSON は key をダブルクォートで囲む

JavaScript で提供しているメソッド

- JSON.stringify...オブジェクトを JSON に変換する
- JSON.parse...JSON をオブジェクトに変換する

```
const student = {
    name: "太郎",
    age: 20,
    gender: "男性",
    skills: ["html", "css", "js"],
    wife: null,
};

const json = JSON.stringify(student);

console.log(student); // skillsがarray(3)で出力される
console.log(json); // skillsが"skills"[]で出力される
```

## 日付と時刻

### Date オブジェクト

JavaScript で用意されており、特定の時刻を表すオブジェクトを得られる。

### インスタンス生成

Date オブジェクトを使用するためにはインスタンスを生成する必要がある。  
インスタンス = 具体的なもの  
Date オブジェクトのインスタンスには３つの生成方法がある。

- new Date()...現在時刻の Date オブジェクトのインスタンスを生成。

```
const dt = new Date();
console.log(dt); // 出力結果：現在時刻
```

- new Date(value)...引数に文字列を指定することでインスタンスを生成。

```
const dt = new Date('2020/12/01 12:34:56');
console.log(dt); // 出力結果：引数に指定した日時
```

- new Date(year,month,date,hours,minutes,secondes)...年、月、日、時、分、秒、ミリ秒を数値で指定してインスタンスを生成する。  
  ※必須は年、月のみ。  
  ※月は 0 から始まる。　１月 = 0, ２月 = 1

```
const dt = new Date(2020,0,15,22,30,30);
console.log(dt); // 出力結果：Jan 15 2020 22:30:30
```

### インスタンスから各種値の取得

- 年

```
const dt = new Date();
const year = dt.getFullYear(); // 取得して定数に格納
console.log(year);
```

- 月...`dt.getMonth()+1`  
  ※１月が 0，12 月が 11 なので+1 をする

- 日...`dt.getDate()`

- 曜日

```
const dt = new Date();
// 日曜が0、土曜が6なので配列を使い曜日に変換する
const dateT = ["日", "月", "火", "水", "木", "金", "土"];
const day = date[dt.getDay()];
console.log(day);
```

- 時...`dt.getHours()`
- 分...`dt.getMinutes()`
- 秒...`dt.getSeconds()`

### 日付の計算

- 1 年後・1 年前

```
const dt = new Date();

dt.setFullYear(dt.getFullYear() + 1);
console.log(dt); // 出力結果：一年後

dt.setFullYear(dt.getFullYear() - 1);
console.log(dt); // 出力結果：一年前
```

- 1 ヵ月後・1 か月前

```
dt.setMonth(dt.getMonth() + 1);
dt.setMonth(dt.getMonth() - 1);
```

- 1 日後・1 日前

```
dt.setDate(dt.getDate() + 1);
dt.setDate(dt.getDate() - 1);
```

- 1 時間後・1 時間前

```
dt.setHours(dt.getHours() + 1);
dt.setHours(dt.setHours() - 1);
```

- 1 分後・1 分前

```
dt.setMinutes(dt.getMinutes() + 1);
dt.setMinutes(dt.getMinutes() - 1);
```

- 1 秒後・1 秒前

```
dt.setSeconds(dt.getSeconds() + 1);
dt.setSeconds(dt.getSeconds() - 1);
```

### day.js

日付処理を扱いやすくしたもの

#### セットアップ

html の head タグ内、title の上に以下の script タグ追加。

```
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://unpkg.com/dayjs"></script>
    <script src="https://unpkg.com/dayjs@1.7.7/locale/ja.js"></script>
    <title>dayjs</title>
</head>
<body>
    <script src="js/main.js"></script>
</body>
</html>
```

day.js を使うファイルに言語の設定を記述

```
dayjs.locale('ja');

// これ以降に処理記述
```

#### 値の取得

- 年

```
const year = day.js().year();
console.log(year);
```

- 月...`dayjs().month() + 1`
- 日...`dayjs().date()`
- 曜日

```
const day = dayjs().day();
const dayT = ["日", "月", "火", "水", "木", "金", "土"];
console.log(dateT[day]);
```

- 時...`dayjs().hour()`
- 分...`dayjs().minutes()`
- 秒...`dayjs().seconds()`

#### format メソッド

()内に自由に文字列を入れて日時の表示形式を指定できる。

```
const today = dayjs().format('YYYY年MM月DD日 dddd HH:mm:ss');
console.log(today);
```

#### 日付の加算・減算

add・subtract メソッドを使用し、第一引数に加・減算する値を、第二引数に加・減算を適用する単位を指定する。

```
const addYear = dayjs().add(1, 'year').format('YYYY/MM/DD HH:mm:ss');

const subtract = dayjs().subtract(1, 'year').format('YYYY/MM/DD HH:mm:ss');
```

## DOM 操作

DOM...JavaScript から HTML を自在に操作するための機能や規則。  
例）画像の切り替え、ボタンクリックによるアラート表示など。

- DOM ツリー...要素が入れ子になっている DOM の構造のこと。
- ノード...DOM ツリーを構成する一つ一つのオブジェクト。階層が上のノードを親ノード、下を子ノード、同階層を兄弟ノードという。
- DOM 操作...DOM ツリーの中のノードを操作すること。

### 要素の取得

| メソッド                               | 説明                                  |
| -------------------------------------- | ------------------------------------- |
| document.getElementById(id)            | id で要素を取得                       |
| document.getElementsByClassName(class) | class で要素を取得                    |
| document.getElementsByName(name)       | name 属性で要素を取得                 |
| document.getElementsByTagName(tagname) | タグ名で要素を取得                    |
| document.querySelector(selector)       | セレクターで要素（最初の 1 つ）を取得 |
| document.querySelectorAll(selector)    | セレクターで要素（複数）を取得        |

### ノードウォーキング

ノードを起点に相対的に別ノードを取得する方法
|プロパティ| 説明|
|---------|--------|
|parentNode| 親ノードを取得|
|childNodes |子ノード群を取得|
|firstElementChild| 最初の子ノードを取得|
|lastElementChild |最後の子ノードを取得|
|previousSibling| 直前のノードを取得|
|nextSibling |直後のノードを取得|

### 属性の取得・設定

- 属性...id,class,テキストなど、要素の構成要素となるもの。

#### プロパティとして取得・設定

| プロパティ               | 説明                       |
| ------------------------ | -------------------------- |
| className                | クラス名の取得             |
| id                       | id 名の取得                |
| textContent/innerContent | ノード内のテキストの取得   |
| innerHTML                | ノード内の HTML 要素の取得 |
| style                    | 要素の style の属性の取得  |

#### 関数の返り値として取得・設定

引数に属性名を入れることで取得

```
getAttribute([属性名]);
getAttribute([属性名],[値])
```

### イベント

ボタン押下、マウスホバー、テキストボックス内の内容変更などで発火。
|プロパティ| 説明|
|---------|-----|
|DOMContentLoaded| Web ページの読み込みが完了した時に発動|
|click |マウスボタンをクリックした時に発動|
|change| フォーム部品の状態が変更された時に発動|
|scroll |画面がスクロールした時に発動|

`element.addEventListener([イベントの種類],[実行したい処理])`

## デバッグ方法
### エラーの種類
* Uncaught SyntaxError: Unexpected token 記号
    * エラーの理由：予期しないトークンが見つかった
    * よくある原因：文法ミス、括弧忘れ、カンマの位置がおかしい等
    * エラー内容：`Uncaught SyntaxError: Unexpected token ',' (at main.js:1:12)`

* Uncaught SyntaxError: Unexpected end of input
    * エラーの理由：予期しない入力の終わりが見つかった
    * よくある原因：開き括弧に対して閉じ括弧の数があってない
    * エラー内容：`Uncaught SyntaxError: Unexpected end of input (at main.js:6:1)`

* Uncaught ReferenceError: 変数名や関数名 is not defined
    * エラーの理由：変数名や関数名が定義されていない
    * よくある原因：タイポ/ライブラリが読み込めてない
    * エラー内容：`Uncaught ReferenceError: number is not defined at (main.js:3:13)`

* Uncaught TypeError: Cannot read property ‘プロパティ名’ of undefined
    * エラーの理由：undefinedのプロパティ名にアクセスしている
    * よくある原因：
        * オブジェクトに存在しないプロパティの何かにアクセスしている
        * 配列のインデックスの番号間違い
        * 関数のreturn忘れ
    * エラー内容：`Uncaught TypeError: Cannot read properties of undefined (reading 'age') at main.js:7:26`

* Uncaught TypeError: オブジェクト.メソッド名 is not a function
    * エラーの理由：関数以外のオブジェクトを関数呼び出ししている
    * よくある原因：undefinedを関数呼び出ししている
    * エラー内容：`Uncaught TypeError: number.map is not a function at main.js:6:8`

* Uncaught TypeError: Cannot read property ‘プロパティ名’ of null
    * エラーの理由：nullのプロパティ名にアクセスしている
    * よくある原因：何かを探す関数などでnullが返ってきている
    * エラー内容：`Uncaught TypeError: Cannot read properties of null (reading 'num1') at main.js:3:20`

## 例外処理
エラーが発生した場合に行う処理。エラーの有無によって処理を分けることができる。

### try...catch構文
* tryブロック内で例外が発生するとcatch説に処理が移行する
* catch節がtryブロック内で発生したエラーオブジェクトと共に呼び出される
* finally節は例外の発生有無にかかわらずtry文の最後に実行される
* catch節とfinally節はどちらか片方省略可能
```
try {
  console.log("try節:この行は実行されます");
  undefinedFunction();
  console.log("try節:この行は実行されません");
} catch (error) {
  console.log("catch節:この行は実行されます");
  console.log(error.message);
} finally {
  console.log("finally節:この行は実行されます");
}
```

### throw文
* ユーザーが例外を投げることができる
* 例外として投げられたオブジェクトはcatch節で関数の引数のようにアクセスできる
```
try {
  throw new Error("例外が投げられました");
} catch (error) {
  console.log(error.message);
}
```