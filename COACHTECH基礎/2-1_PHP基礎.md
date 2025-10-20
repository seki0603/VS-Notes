---
tags:
  - PHP
---

# プログラミングの基本構造

- 順次進行　上から下へ順番に処理を実行していく動き
- 条件分岐　ある条件によって処理の流れが分かれる動き
- 繰り返し　ある条件が満たされるまで処理を何度も実行する動き

<br><br>

# 基本的なルール

PHP のコードは「」で終わるのが基本

```
<?php
  echo "こんにちは";
?>
```

ファイルの中に PHP コードしか書かれていないときは  
最後の?>は省略できる。  
HTML の中に PHP を差し込むときは省略できない。

<br><br>

# 変数と定数

## 変数

プログラムの中でデータ（値）を入れておく箱のようなもの。  
<br>

## 変数のルール

- 変数名は「$」から始まる。
- 変数名は大文字と小文字を区別する。
- 変数に使える文字

```
英大文字小文字（a～zもしくはA～Z）
数字（ただし、先頭には使えない）
_（アンダースコアー）
```

- 変数には１つのデータしか入れられない。  
  ※プログラムは上から下に流れるため、後から代入したデータが上書きするから。

<br>

## 定数

税率のように固定の値を表現するもの。  
変数のようにプログラム最中で書き換えられることがない。

<br>

## データ型

PHP の変数や定数には様々なデータを入れられる。

<br>

### 文字列-string

「''」または、「""」で括られている必要がある

```
<?php
$item = "PHP";
```

<br>

### 整数-integer

「''」や「""」で括る必要がない

```
<?php
$item = 123;
```

<br>

## 連結演算子 「.」

文字列や変数をくっつけることで加工や出力をすることができる。

```
<?php
$name1 = "Sato" . "Taro";
$name2 =  "Tanaka";
$last_name = "Yamada";
$first_name = "Saburo";

echo $name1;
echo "<br />";
echo $name2 . "Jiro";
echo "<br />";
echo $last_name . $first_name;
```

ブラウザ表示

```
SatoTaro
TanakaJiro
YamadaSaburo
```

PHP では改行`<br>`のように HTML のタグを挿入することができる。

<br><br>

# 演算子

## 算術演算子

数値の計算を行うときに使う

- $a + $b　　$a と $b を足し算した結果
- $a - $b　　$a から $b を引き算した結果
- $a * $b　　$a と $b を掛け算した結果
- $a / $b　　$a を $b で割った結果
- $a % $b　　$a を $b で割った余り

<br>

## 代入演算子と複合演算子

- 代入演算子　　変数に値や計算結果を代入するための演算子
- 複合演算子　　代入演算子と算術演算子を組み合わせたもの

<br>

- $a = $b　　　$a に$b の値を代入
- $a += $b　　$a に$bの値を加算して代入（$a = $a + $b と同じ）
- $a -= $b　　$a に$b の値を減算して代入（$a = $a - $b と同じ）
- $a *= $b　　$a に$b の値を乗算して代入（$a = $a \* $b）
- $a /= $b　　$a に$b の値を除算して代入（$a = $a / $b）
- $a %= $b　　$a に$b の値を剰余算して代入（$a = $a % $b）
- $a .= $b　　　$a に$b の文字列を連結して代入（$a = $a.$b に等しい）

<br>

## 比較演算子

数値や変数の値を利用して条件式を作り、その結果によって処理を変えるときに使う。  
条件が成立した場合を「真(TRUE)」、成立しない場合を「偽(FALSE)」と呼ぶ。

- $a == $b　　　$a が $b に等しい時に TRUE
- $a === $b　　$a が $b に等しく同じ型である場合に TRUE
- $a != $b　　　$a が $b に等しくない場合に TRUE
- $a <> $b　　　$a が $b に等しくない場合に TRUE
- $a !== $b　　　$a が $b と等しくないか、同じ型でない場合に TRUE
- $a < $b　　　　$a が $b より少ない時に TRUE
- $a > $b　　　　$a が $b より多い時に TRUE
- $a <= $b　　　$a が $b より少ないか等しい時に TRUE
- $a >= $b　　　$a が $b より多いか等しい時に TRUE

<br>

## 論理演算子

複数の条件を組み合わせて、より複雑な条件を表すときに使う。

- ! $a　　　　　　$a が TRUE でない場合 TRUE
- $a && $b　　　$a および $b が共に TRUE の場合に TRUE
- $a || $b　　　　$a または $b のどちらかが TRUE の場合に TRUE
- $a and $b　　　$a および $b が共に TRUE の場合に TRUE
- $a or $b　　　　$a または $b のどちらかが TRUE の場合に TRUE
- $a xor $b　　　$a または $b のどちらかが TRUE でかつ両方とも TRUE でない場合に TRUE
- $a ?? $b　　　　$a が null や未定義の場合、$bを返しそれ以外の場合は$a を返す

<br>

## 加算子と減算子

数値に１を加えることを加算子、１を減じることを減算子という。  
前置は変数の参照より先に、後置では変数の参照後に演算を行う。

- ++$a　　　$a に 1 を加え、$a を返します。
- $a++　　　$a を返し、$a に 1 を加えます。
- --$a　　　$a から 1 を引き、$a を返します。
- $a--　　　$a を返し、$a から 1 を引きます。

<br><br>

# 条件分岐

## if 文

```
if( 条件 ){
// 真の時に実行される処理
}else{
// 偽の時に実行される処理
}
```

複数の条件のどれに当てはまるかによって、それぞれ異なる処理を行いたい時は、  
`elseif`を使う。

<br>

## switch 文

if 文は TRUE か FALSE による判断だが、switch は式の条件式の結果、  
またそれを複数指定することができる。

```
switch (値) {
    case 条件1:
        // 条件1が真の時に実行される処理
        break;
    case 条件2:
        // 条件2が真の時に実行される処理
        break;
    default:
        // どの条件にも当てはまらない時に実行される処理
        break;
}
```

<br>

## 三項演算子

if 文と似た使い方ができる条件演算子。「?」「:」を組み合わせて使う。  
`$result = (条件式) ? "TRUE時の値" : "FALSE時の値";`

<br><br>

# 繰り返し

## for 文

繰り返す条件を（）のなかで定義して、処理を繰り返す。  
<br>
基礎構文

```
for ($i = 初期値; $i <= 回数; 増減式) {
処理
}
```

例

```
<?php
for ($i = 0; $i < 4; $i++) {
  echo $i;
}
```

出力内容

```
0123
```

<br>

## while 文

ある条件が成り立っている間だけ処理を繰り返し実行する。  
for 文のようにカウンターにあたる部分がなく、条件判定を行う変数は  
繰り返しの処理の中で変化させてループ回数を制御する。  
<br>
ループ処理からに懈怠場合は break、処理をスキップしたい場合は continue を使う。  
<br>
基礎構文

```
while (条件) {
// 真の時に実行
// 繰り返しの処理の中で変数の値を変化させる
}
```

例

```
<?php
$i = 0;

while ($i < 3) {
  echo 'i = ' . $i . '<br />';
  $i += 1;
}
```

出力内容

```
i = 0
i = 1
i = 2
```

<br>

### break の使い方

```
<?php
$i = 0;
while ($i < 10) {
  if ($i == 5) {
    break;
    // $iが5の時、ループから抜ける。
  }
  echo $i;
  $i++;
}
```

出力結果

```
01234
```

<br>

### continue の使い方

```
<?php
$i = 0;
while ($i < 10) {
  if ($i == 5) {
    $i++;
    continue;
    // $iが5の時、$iに1を足す処理をし、スキップをする。
  }
  echo $i;
  $i++;
}
```

出力結果

```
012346789
```

<br>

## do...while 文

通常の while 文同様、条件式が false になるまで繰り返し処理を実行する。  
do...while 文は繰り返し処理を行ってからその処理を評価するため、  
少なくとも一回は繰り返し処理が行われる。  
<br>
基礎構文

```
do{
// whileの条件式が真の時に実行
}while (条件式);
```

例

```
<?php
$i = 0;
do {
  echo $i . '<br />';
  $i++;
} while ($i < 5);
```

出力結果

```
0
1
2
3
4
```

<br><br>

# 関数

- 関数　　ひと纏りの処理を行う機能
- 関数を定義する　　関数の機能を記述すること
- 引数（パラメータ）　　処理の材料となる値
- 戻り値（返り値）　　結果の値

<br>
関数は定義するだけでは機能せず、呼び出されて初めて処理が実行される。  
渡された引数を指示通りに処理し、結果を返してくれる箱のようなイメージ。  
<br>

## 関数の種類

- 内部関数（ビルトイン関数）  
  　 PHP にあらかじめ用意されている関数。var_dump や array など。
- ユーザー定義関数  
  　自分で定義した関数。内部関数など PHP のプログラムとして有効なものを使用できる。  
  <br>

## 関数の定義

```
function 関数名(){
処理内容
return 返り値
}
```

関数名、引数名は基本自由だが、関数名はビルド関数で使用されたものは使えない。  
<br>

## 関数定義の種類

### 引数と戻り値あり

```
<?php
function outputNumber($a)
{
  echo "引数の値は" . $a . "です";
  return;
}

outputNumber(2);
```

関数で返す値のない時は戻り値を指定する必要はない。  
戻り値のない return は省略可能。  
<br>

### 引数と戻り値なし

```
<?php
function outputHello()
{
  echo "Hello world";
}

outputHello(); // ①
```

① で関数の呼び出しをしている。  
呼び出された関数 outputHello が Hello world と出力させる。  
<br>

### 引数と戻り値あり

```
<?php
function text($number1, $number2)
{
  $value = $number1 + $number2;
  return $value;
}

$total = text(2, 4);
echo $total;
```

変数 number1 には 2 が、number2 には 4 がそれぞれ渡されている。  
変数 total に、関数 text の戻り値が代入されている。

<br><br>

# 配列と連想配列

変数をデータを入れる箱に例えると、配列は箱が連なったようなもの。  
変数は一つのデータしか入れられないが、配列はつながっている箱の数だけデータを入れることができる。  
<br>

## 配列のルール

変数と同じく＄から始まり、配列名に使える文字も同じ。  
<br>

```
<?php
$people = array('Taro', 'Jiro', 'Saburo');

var_dump($people);
```

出力結果

```
array(3) {
[0]=> string(4) "Taro"
[1]=> string(4) "Jiro"
[2]=> string(6) "Saburo"
}
```

「var_dump」関数は与えられた変数の値を詳細に出力する。  
出力結果には配列の要素数と各要素のデータ型、値、値の長さが含まれる。  
これにより、デバックに役立てることができる。

<br>

## 配列の番号

配列要素は 0 から始まる番号が付けられる。
配列名[番号]とすることで個別の値にアクセスできる。
<br>

## 連想配列

添字に文字列を用いた配列。

```
<?php
$people = array(
  'person1' => 'Taro',
  'person2'  => 'Jiro',
  'person3'  => 'Saburo'
);

var_dump($people);
```

出力結果

```
array(3) { ["person1"]=> string(4) "Taro" ["person2"]=> string(4) "Jiro" ["person3"]=> string(6) "Saburo" }
```

別の作成方法

```
$people = [
  'person1' => 'taro',
  'person2' => 'jiro',
];
```

配列の値を取得するには、配列の変数にキーを[]で指定する。

```
echo $people['person1'];
```

<br>

## 多次元配列

配列の中に配列が入っている状態のこと

```
<?php
$people = [
  [
    "last_name" => "山田",
    "first_name" => "太郎",
    "age" => 29,
    "gender" => "男性"
  ],
  [
    "last_name" => "鈴木",
    "first_name" => "次郎",
    "age" => 25,
    "gender" => "男性"
  ],
  [
    "last_name" => "佐藤",
    "first_name" => "花子",
    "age" => 20,
    "gender" => "女性"
  ]
];
```

出力するときは、同じように配列のキーを[]で指定する。

```
echo $people[0]["last_name"];
```

<br>

## foreach 文

配列の要素の数だけ処理が繰り返し行われる。  
<br>
例

```
<?php
$people = array('Taro', 'Jiro', 'Saburo');

foreach ($people as $person) {
  echo $person;
  echo '<br />';
}
```

出力結果

```
Taro
Jiro
Saburo
```

配列のループ処理の場合に添字を取得する方法

```
<?php
$people = array(
  'person1' => 'Taro',
  'person2'  => 'Jiro',
  'person3'  => 'Saburo'
);

foreach ($people as $person => $name) {
  print $person . "は" . $name . "です" . '<br />';
}
```

配列をキー => 値（$person => $name）にすると、  
元の配列にキーが指定されてない場合はデフォルトで添字が当てられる。

<br><br>

# データの受け取り

## データ送信方法

- GET：データが URL で引き渡される
- POST：データが URL で引き渡されない

<br>

### POST

HTML

```
<form action="course.php" method="POST">
  <label>会社名：<input type="text" name="company"></label>
  <br />
  <input type="submit" name="submit" value="送信する" />
</form>
```

入力ページから送信をクリックすると、表示画面に遷移する。
遷移先ページのファイル名を action に指定する。  
<br>
データを受け取る PHP ファイルの作成

```
<?php
$company = htmlspecialchars($_POST['company'], ENT_QUOTES);
print "会社名は" . $company . "ですね";
```

<br>
POSTで送信する場合、Webブラウザの入力データはWebサーバーへと送られる。  
「method="POST"」で送信したデータをPHPプログラムで受け取る場合に  
使う変数は$_POST  
<br>

### GET

HTML

```
<form action="course.php" method="GET">
  <label>会社名：<input type="text" name="company"></label>
  <br />
  <input type="submit" name="submit" value="送信する" />
</form>
```

```
<?php
$company = htmlspecialchars($_GET['company'], ENT_QUOTES);
print "会社名は" . $company . "ですね";
```

<br>
ブラウザの挙動は同じだが、遷移した音のURLは以下のようになる。  
`http://localhost/php01/course.php?company=株式会社estra&submit=送信する`  
<br>
GETで送信する場合、送信データはURLのファイル名の後ろに付加される。  
PHPでデータを受け取った時は、変数「$_GET」に格納される。  
<br>

## POST と GET の使い分け

以下の場合は POST を使用

- サーバーに情報を投稿する
- 配列などの長いデータを送信する
- ファイルのアップロードを行う

<br>

以下の場合は GET を使用

- サーバーから情報を取り出す
