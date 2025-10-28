# localstorage 保存によるデータの永続化

## 入力・表示部分作成

```
<body>
    <div class="container" id="container" >
        <h1 class="title">JavaScript Todo List</h1>
        <input class="text-input" id="text-input" type="text" placeholder="Todoを入力してください">
        <ul class="todo-list" id="todo-list"></ul>
    </div>
</body>
```

<br><br>

## 必要な値を取得

```
const textInput = document.getElementById('text-input');
const todoList = document.getElementById('todo-list');
```

<br><br>

## イベントを作成

キーダウンや、ボタンクリックなどで発火するイベントを作成

```
textInput.addEventListener('keydown', enter => {
    // inputの入力値を文字前後の空白を除いて取得
    const text = textInput.value.trim();

    // 文字の入力がない時とEnter以外のキーダウンに対しては何もしない
    if (text === '' || enter.key !== 'Enter') {
        return;
    }

    // localstorage保存
    saveLocalStorage(text);
    // Todo作成処理（後に内容を定義）
    createTodoItem(text);
    // Todoを追加した後は入力値を空に
    textInput.value = '';
});
```

<br><br>

## 保存対象となるデータの作成処理を記述

```
function createTodoItem(text) {
    const li = document.createElement('li');
    const span = document.createElement('span');
    const button = document.createElement('button');

    li.classList.add('list-item');
    span.classList.add('todo-text');
    button.classList.add('delete-button');

    span.textContent = text;

    button.textContent = '削除';
    button.type = 'button';

    li.appendChild(span);
    li.appendChild(button);
    todoList.appendChild(li);

    // 削除ボタン押下時の処理も作成
    button.addEventListener('click', () => {
        removeLocalStorage(text);
        li.remove();
    });
}
```

<br><br>

## localstorage 保存処理を記述

```
function saveLocalStorage(text) {
    const listItem = JSON.parse(localStorage.getItem('list-item')) || [];
    listItem.push(text);
    localStorage.setItem('list-item', JSON.stringify(listItem));
}
```

<br><br>

## 保存したデータの再描画処理を記述

```
document.addEventListener('DOMContentLoaded', getLocalStorage);

function getLocalStorage() {
    // localStorage内にデータがなければ空配列を生成する
    // → nullチェックを省略できるように || [] で初期化
    const listItem = JSON.parse(localStorage.getItem('list-item')) || [];
    listItem.forEach(text => createTodoItem(text));
}
```

### DOMContentLoaded について

HTML の読み込みと DOM 構築が完了したタイミングで発火するイベント。画像などの読み込みを待たずに実行できるため、初期表示処理に適している。
<br>

- ここまでで、サイトを一度離れ再訪しても、表示がキープされるようになる。
- 再描画されたデータに削除イベントが定義されていること。
  - 上記の場合は呼び出した createTodoItem()内に削除イベントが定義されているため、再描画されたデータにも削除イベントが付与される。
    <br><br>

## localstorage からの削除処理を記述

```
// localstorageから削除
function removeLocalStorage(text) {
    const listItem = JSON.parse(localStorage.getItem('list-item')) || [];
    const index = listItem.indexOf(text);
    if (index !== -1) {
        listItem.splice(index, 1);
        localStorage.setItem('list-item', JSON.stringify(listItem));
    }
}
```

- localstorage からの削除を記述しないと、画面上で削除イベントが発火してもデータは残っているため、再訪時に再描画される。
- UI 上の削除イベントとデータの削除は別物であり、連動させるには両方記述する必要がある。
  <br><br>

## 総括

- localStorage は「UI の状態を保存する仕組み」ではなく、「データそのものを保存する仕組み」である。
- UI の変化（表示・削除など）は別途 DOM 操作で再現する必要があるため、両者を連動させるコード構造を意識する。
