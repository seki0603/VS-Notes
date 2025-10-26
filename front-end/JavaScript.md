# JavaScript

## 変数と定数

- 変数...let
- 定数...const

原則として const を使い、値を再代入しないといけない場合のみ使う。

### 変数の命名規則

- １文字目に数字は使用できない
- ,=,&などの記号は使用できない

### コード構造

- 文（命令文）...アクションを実行するコマンド。「;」で区切る。上から順に実行される。
- コメント...コードを使った理由などを残す。「//」から始まる。

## データ型

- number（数値）...整数と少数の両方に使われる。
- string（文字列）...引用符で囲む必要がある。
  |引用符|名称|特徴|
  |-----|----|----|
  |"Hello"|ダブルクォート|シングルクォートと違いなし|
  |'Hello'|シングルクォート|ダブルクォートと違いなし|
  |`Hello`|バッククォート|定数を埋め込める|

```
const str1 = "Hello";
const str2 = 'Hello world';
const str3 = `${str1} world`;
```

- boolean（論理型）...yes/no(true/false)の２つの値を持つ。
- null...「無し」、「空」または「不明な値」
- undefined...値が代入されていないこと表す。

### typeof による操作

typeof x とすることで console で型が返ってくる。

### 0,null,undefined の違い

- 0...0 という値が代入されている状態。
- null...値が代入されていない状態。
- undefined...定義されていない状態。

### 型変換

JavaScript では自動で型変換を行う。手動でも可能。

- 文字列変換

```
const bool = true;
console.log(typeof bool);
const str = String(bool); //String()で値を文字列に変換することができる
console.log(typeof str);
```

- 数値変換

```
const str = "123";
console.log(typeof str);
const num = Number(str); //Number()で値を数値に変換することができる
console.log(typeof num);
```

- boolean 変換
  0,null,undefined、空文字("")などは false になる。他は true。

```
console.log(Boolean(1));
console.log(Boolean(0));
console.log(Boolean("hello"));
console.log(Boolean(""));
```

## 演算子

### 算術演算子

- 加算: +
- 減算: -
- 乗算: \*
- 除算: /

```
const add = 6 + 2;
console.log(add);
```

### 剰余演算子％

a % b = a を b で割ったあまり

### インクリメント・デクリメント

- ++（インクリメント）...変数を１増加させる。
- --（デクリメント）...変数を１減少させる。

```
let inc = 2;
inc++;
console.log(inc);
```

### 比較演算子

| 演算子 | 意味                    |
| ------ | ----------------------- |
| a == b | a と b が同じ値         |
| a != b | a と b が同じ値ではない |
| a < b  | a が b より小さい       |
| a <= b | a が b 以下             |
| a > b  | a が b より大きい       |
| a >= b | a が b 以上             |

### 論理演算子

- a && b...a と b の両方が成立する
- a || b...a か b のどちらか１つが成立する
- !a...a 以外なら成立する |
- a ?? b...a が null または undefined なら b が成立する

### 厳密等価

データ柄も一致するか比較演算子

```
1 === 1 //true
'1' === 1 //false
1 !== 1 //false
'1' !== 1 //true
```

## 条件分岐

### if 文

```
const price = 1800;

if (price < 1000) {
  console.log("安い");
} else if (price > 2000) {
  console.log("高い");
} else {
  console.log("丁度良い");
}
// 出力結果：丁度良い
```

### 参考演算子

`(条件式) ? “true(trueに変換できる値)” : “false(falseに変換できる値)”`

```
const quantity = 400;

const banana = (quantity <= 300) ? "少ない" : "多い";
console.log(banana);
// 出力結果：多い
```

### switch 文

```
const person = 3;

switch (person) {
    case 1:
        console.log("太郎さん");
        break;
    case 2:
        console.log("次郎さん");
        break;
    case 3:
        console.log("三郎さん");
        break;
    case 4:
        console.log("四郎さん");
        break;
    case 5:
        console.log("五郎さん");
        break;
}
// 出力結果：三郎さん
```

## 配列
`const array = ["太郎", "次郎", "三郎", "四郎", "五郎"];`  
一番最初の要素に0、二番目の要素に1...という順に番号が振られる。
「配列名[番号]で個別の値にアクセスできる。
```
console.log(array); // 出力結果：配列全部
console.log(array[0]); //出力結果：太郎
console.log(array[1]);
console.log(array[2]);
console.log(array[3]);
console.log(array[4]);
```

### 配列の置き換え
```
const array = ["太郎", "次郎", "三郎", "四郎", "五郎"];

array[1] = "二郎"; // 番号で置き換える要素を指定

console.log(array);
```

### 要素の総数
lengthで取得できる
```
const array = ["太郎", "次郎", "三郎", "四郎", "五郎"];

console.log(array.length); // 出力結果：5
```

### 要素の追加・削除
* push...要素を配列の末尾に追加
* unshift...要素を配列の先頭に追加
* pop...配列の末尾の要素を削除
* shift...配列の先頭の要素を削除
* splice...指定の場所の要素を削除
```
const array = ["太郎", "次郎", "三郎", "四郎", "五郎"];

array.splice(1, 3); //２番目（次郎）から3つ削除

console.log(array); // 出力結果：太郎・五郎
```

### 配列同士の結合
concatまたは...(スプレッド構文)で配列と配列を結合できる。
```
const array = ["a", "b", "c"];

const newArray = ["x", "y", "z", ...array];
// または
const newArrayConcat = ["x", "y", "z"].concat(array);

console.log(newArray); // 出力結果：x,y,z,a,b,c
console.log(newArrayConcat); // 出力結果：x,y,z,a,b,c
```

Spread構文は配列の任意の場所に配列を展開できる。
```
const array = ["a", "b", "c"];

const newArray = ["x", ...array, "z"];

console.log(newArray); // 出力結果：x,a,b,c,z
```

### 配列から要素の検索
* indexOf...配列のどの位置に要素があるか知りたい場合。  
指定された要素の最初の番号（インデックス）を返し、要素が無い場合は-1を返す。

* find...関数を満たす配列内の最初の要素を取得する場合。
```
const array = [10, 30, 5, 40];

const found = array.find(function (element) {
    return element > 20
});

console.log(found); // 出力結果：30
```

* slice...配列から指定範囲の要素を取り出す場合。
```
const array = ["a", "b", "c", "d", "e"];

// インデックス2から4の範囲を取り出す
console.log(array.slice(2, 4)); // 出力結果：c,d

// 第二引数を省略した場合は、第一引数から末尾の要素までを取り出す
console.log(array.slice(1));　// 出力結果：b,c,d,e
```

* includes...要素が含まれているかを取得する場合
```
const array = ["HTML", "CSS", "JavaScript", "PHP"];

// includesは含まれているならtrueを返す
if (array.includes("JavaScript")) {
    console.log("配列にJavaScriptが含まれています");
}
```

### 配列を反復処理する関数
* forEach
配列の全要素に対して関数を実行することができる。
第一引数(item)に要素が入り、第二引数(index)に要素の番号が入る。
```
const array = ["太郎", "次郎", "三郎", "四郎", "五郎"];

array.forEach(function (item, index) {
    if (item == "三郎") {
        console.log(`${item}は${index}番目の要素です`);
        // 出力結果：三郎は2番目の要素です
    }
});
```

* map
配列の全要素に対して関数を実行し、処理結果を配列に格納することができる。
```
const array = [8, 10, 5, 3, 2];

const result = array.map(function (item) {
    return item * 2;
});

console.log(result); // 出力結果：16, 20, 6, 4
```

* filter
配列の全要素に対して関数を実行し、条件に合致した配列のみを格納する。
```
const array = [8, 10, 5, 3, 2];

const result = array.filter(function (item) {
    return item % 2 === 1;
});

console.log(result); // 出力結果：Array(2) (5と3が格納されている)
```

## 関数
繰り返される処理をまとめて定義する。
```
function 関数名(引数) {
    処理内容

    return 返り値;
}
```
```
function text(number1, number2) {
    const value = number1 + number2;
    return value;
}

const sum = text(2, 4);
console.log(sum);
```

### 関数式
関数を作るための別の構文
```
const text = function (number1, number2) {
    const value = number1 + number2;
    return value;
}

console.log(text (2,4));
```

### アロー関数
関数を作成するための、よりシンプルな構文。
```
const text = (number1, number2) => {
    const value = number1 + number2;
    return value;
}

console.log(text(2, 4));
```

### コールバック関数
関数の引数に入れられた関数。関数の中で関数を実行するために使用する。
```
function vegetable(name, price, func) {
    const pit = func(price);
    console.log(name + "の税込価格は" + pit + "です");
}

// 関数式
const priceIncludingTax = function (price) {
    const tax = 1.1;
    return Math.floor(price * tax);
}
vegetable("苺", 200, priceIncludingTax);
// priceIncludingがコールバック関数
```
※Math.floor = 小数点を切り捨てて整数にする。

### スコープ
定義した変数や定数を参照できる範囲。
```
const globalConst = 'globalConst';
let globalLet = 'globalLet';

function dummyFunc() {
    // ローカルスコープ内
    const localConst = 'localConst';
    let localLet = 'localLet';
}

dummyFunc();

console.log(globalConst);
console.log(globalLet);

// 値の更新
globalLet = 'updateGlobalLet';
console.log(globalLet);

console.log(localConst); // 出力できない
console.log(localLet);　// 出力できない
```
ローカルスコープ内で定義された定数と変数はスコープ外で参照できない。

### export/import
* export...記述した関数や変数を別のファイルで呼び出すことができる。
```
export function popup(input) {
    console.log(input);
}
```

* import...exportを記述した他のファイルを呼び出すことができる。
```
// メソッドとファイル名を記述
import {popup} from './export.js';

popup("hello");
```

## 繰り返し
### for文
for文では３つの式を与える。
* 初期値：繰り返す際の初期値
* 条件式：繰り返しを継続する条件
* 増減式：「初期値」を増減する式
```
for (初期値; 条件式; 増減式) {
    // 処理内容
}
```

```
const lists = ["太郎", "次郎", "三郎", "四郎", "五郎"];

for (let i = 0; i < lists.length; i++) {
    console.log(lists[i]);
}
```

### while文
回数の決まっていない繰り返し処理を行う際に記述。
```
while (条件式) {
    // 処理内容
}
```

### for文とwhile文の使い分け
* for文...繰り返し回数が決まっている場合
* while文...繰り返し回数が決まってない場合  
（条件を満たすまで、ずっと実行される。）

### do...while文
指定された処理を、whileに記述した条件式がfalseになるまで繰り返す。
```
do {
    // 処理内容
} while (条件式);
```
* while文...評価してから処理をする。（一回も処理しない場合がある。）
* do...while文...処理してから評価する。（少なくとも一回は処理をする。）
