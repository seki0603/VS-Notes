# 参考外部チートシート
BEM Naming Cheat Sheet  
https://bem-cheat-sheet.9elements.com/  
<br>

Qiita 【命名規則】BEMを使った書き方についてまとめてみた【CSS】  
https://qiita.com/takahirocook/items/01fd723b934e3b38cbbc  

<br><br>

# レイアウト分割
ヘッダー、メイン、フッターや、メインの中のまとまり毎に分ける。  

# パーツ（Element）の洗い出し
分割した一纏りに必要なUIパーツを全部書き出す。

例）
* メッセージ表示欄
* 見出し（タイトル）
* テキスト入力（`<input>`）
* 作成ボタン（`<button>`）
* 更新ボタン
* 削除ボタン
...etc
<br>

# 各パーツをdivタグで囲う
この時、ブラウザ上では目に見えないformタグなど付けていく。
<br>

# formやtableなどをdivで囲うか判断する
BEMにおける囲うべきかどうかの判断フロー  
1. その要素は意味を持つHTMLタグか？（form, table, nav, headerなど）
  * yes -> 2へ
  * no -> divで囲う
2. そのままBlockとして成立するか？
  * yes -> 3へ
  * no -> divで囲う
3. 中央寄せや幅調整は必要ないか？
  * yes -> そのまま
  * no -> divで囲う

<br>


# 関連するパーツをグルーピング
「このパーツたちはひとまとまり」と決めてBlockを作り、class名をBlockまで記述。  

例）
* Todo作成フォーム → create-form
* Todo更新フォーム → update-form
* Todo削除フォーム → delete-form
* Todo一覧テーブル → todo-table
...etc
<br>

# Blockの中のパーツに分ける
それぞれのBlockの中の要素に名前を付ける。  
Block__Element  

例）
```
<form class="create-form" action="">
  <div class="create-form__item">
    <input class="create-form__item-input" type="text" />
  </div>
  <div class="create-form__button">
    <button class="create-form__button-submit">送信</button>
  </div>
</form>
```
<br>

# 必要な個所にのみModifierを命名

# css作成

# レイアウト分割
1. 各ページの共通部分（ヘッダーまでや、フッターなど）で親レイアウト作成
2. 各ページごとに異なる部分に`@yield`を指定
3. 各ページ独自の部分で子レイアウトを作成（共通部分の削除）
4. 子レイアウトの`@yield`にはめ込む各部分を`@section`で囲う
<br>

# css分割