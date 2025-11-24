# プロパティとメソッド
```
const myBlog = {
    title: "JavaScript頑張るぞブログ",
    author: "webの神太郎",
    addPost: () => {
        console.log('記事を更新したよ！');
    },
    deletePost: () => {
        console.log('記事を削除したよ！');
    }
}
```
* オブジェクト...変数の中身(const .myBlogの中身)
* プロパティ...オブジェクトの中に格納した値
    * `プロパティ名: 値`
* メソッド...オブジェクトの中に格納した関数
    * addPostとdeletePost

## 呼び出し方
* `console.log(変数名.プロパティ名);`...値が取り出される
* `定数名.関数名();`...関数が実行される。

# DOM操作（Document Object Model）
HTMLの要素などをオブジェクトとして扱うための仕組み  
HTMLのタグをオブジェクトとして扱う  
JavaScriptによってHTMLを操作できる

## DOM操作の基本メソッド
* `document.getElementById();`...idでHTML要素を取得する
* `document.getElementsByClassName();`...クラス名でHTML要素を複数取得する

## クリックイベントの設定
```
const btn01 = document.getElementById('btn01');
btn01.addEventListener('click', function() {
    console.log(this);
});
```
* 要素を取得して変数に格納
* `取得した要素.addEventListener`でイベントを設定
* addEventListenerの第一引数にイベントの種類を設定
* 第二引数にイベントの内容を関数で設定
    * アロー関数ではなくfunctionキーを使う
※thisキーワード...指定した要素自信を取り出す

## クラス名の追加
`クラスを付与する要素.classList.add('クラス名');`
```
const btn01 = document.getElementById('btn01');
btn01.addEventListener('click', function() {
    this.classList.add('clicked');
});
```
※Idで取得した要素にボタンクリックで`class="clicked"`を追加している

### クラス名のつけ外し
`要素.classList.toggle('クラス名');`
```
const btn01 = document.getElementById('btn01');
btn01.addEventListener('click', function() {
    this.classList.toggle('clicked');
});
```
※クリックのたびにクラスの追加 or 削除を繰り返す

### クラス名の削除
`要素.classList.remove('クラス名');`
```
const btn01 = document.getElementById('btn01');
btn01.addEventListener('click', function() {
    this.classList.remove('clicked');
});
```

## 複数の要素へのイベント設定
```
const btn = document.getElementsByClassName('btn');
for (let i =0; i < btn.length; i++) {
    btn[i].addEventListener('click', function() {
        this.classList.toggle('clicked');
    });
}
```
* クラス名で複数の要素を取得し、変数に格納。
* for文で取得した要素の一つひとつにイベントを設定
    * `要素[i].addEventListener()`
※`要素.length` = 変数の中に入っている要素の数