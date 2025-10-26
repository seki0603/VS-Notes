# 準備
## ビュー側のタグ内ににIdを指定
```
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Todoリスト</title>
    <link rel="stylesheet" href="css/style.css" />
    <script src="js/main.js" defer></script>
</head>
<body>
    <div class="container" id="container" >
        <h1 class="title">JavaScript Todo List</h1>
        <input class="text-input" id="text-input" type="text" placeholder="Todoを入力してください">
        <ul class="todo-list" id="todo-list"></ul>
    </div>
</body>
</html>
```

* JSファイル読み込み`<script src="js/main.js" defer></script>`
* head内に読み込む場合はdeferでHTMLの後で読み込むように指定。
* idはJSでの操作の為に、classはCSSでスタイルを当てるために設定

# 処理記述

```
'use strict';

const textInput = document.getElementById('text-input');
const todoList = document.getElementById('todo-list');

textInput.addEventListener('keydown', enter => {
    // inputの入力値を文字前後の空白を除いて取得
    const text = textInput.value.trim();

    // 文字の入力がない時とEnter以外のキーダウンに対しては何もしない
    if (text === '' || enter.key !== 'Enter') {
        return;
    }

    // タグを追加
    const li = document.createElement('li');
    const span = document.createElement('span');
    const button = document.createElement('button');

    // 作成したタグにクラスを付与
    li.classList.add('list-item');
    span.classList.add('todo-text');
    button.classList.add('delete-button');

    // spanの内容を入力値に
    span.textContent = text;

    // buttonの文字とタイプを指定
    button.textContent = '削除';
    button.type = 'button';

    // liの中にspanとbuttonを入れる
    li.appendChild(span);
    li.appendChild(button);
    // ulの中にliを入れる
    todoList.appendChild(li);

    // Todoを追加した後は入力値を空に
    textInput.value = '';

    // 削除ボタン押下時も処理
    button.addEventListener('click', push => {
        // 押されたボタンの一番近くのliを削除
        todoList.removeChild(push.target.closest('li'));
    });
});
```

## idで要素を取得し、定数に格納
`const 定数名(ローワーキャメル) = document.getElementById('id名');`

## 取得した定数に関する操作を記述
`定数名.addEventListener('イベント', 関数名 => {処理内容});`

### イベント（きっかけとなる操作）
* addEventListener type一覧と各ブラウザ対応...https://qiita.com/mrpero/items/156968e3512d42fffc5e

## 入力値を取得
`const text = textInput.value.trim();`
* value...入力値
* trim()...引数の文字を除く（この場合はスペース入力を除く）

## 操作対象外の条件を設定（バリデーションの代わり）
```
if (text === '' || enter.key !== 'Enter') {
        return;
    }
```
上記の場合、入力値が無い状態でのEnter押下、もしくはEnter以外の押下に非対応（ただただリダイレクト）を指定。

## 処理内容記述
* タグ追加...`document.createElement('タグの種類');`
* タグにクラスを付与...`classList.add('クラス名');`
* タグの内容指定...`タグの種類.textContent = 指定したい内容;`
* タグのタイプ指定（ボタンやインプット）...`タグの種類.type = 'タイプ';`
* タグを入れ子にする...`親にするタグ.appendChild(子にするタグ);`
    * idで指定して格納した定数も利用可能`todoList.appendChild(li);`
* 入力値を空に...`textInput.value = '';`

### 削除処理
```
button.addEventListener('click', push => {
        // 押されたボタンの一番近くのliを削除
        todoList.removeChild(push.target.closest('li'));
    });
```
