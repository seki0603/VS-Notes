# wire:〇〇属性について

## 役割：HTML と PHP をリアルタイムでつなぐ橋

- Livewire は、JavaScript を自分で書かなくても
  ブラウザの入力 → PHP の変数更新 → Blade 再描画
  を自動で行う。

- つまり wire:model や wire:click は、
  「どのタイミングでどの処理を Livewire に伝えるか」を指定する“通信タグ”。
  <br><br>

## wire:model：双方向データバインディング

`<input wire:model="title" type="text">`
<br>

| 方向                              | 内容                                                          |
| --------------------------------- | ------------------------------------------------------------- |
| ユーザーが入力した瞬間            | `$this->title`（Livewire クラス内のプロパティ）に即反映される |
| `$this->title` の値を変更した場合 | 画面上の input の中身も自動で更新される                       |

<br>

- input の中身と PHP のプロパティが常に同期している
<br>

### クラス側
App\Http\Livewire\ComponentName.php  
`public $title;`  
入力欄のmodelの中身とpublic $プロパティが一致していると
* 入力するたびに`$this->title`に値が入る
* `$this->title`を操作すると、inputにも反映される
<br>

### 便利な装飾子
| 属性                 | 意味                               |
| ------------------ | -------------------------------- |
| `wire:model.lazy`  | フォーカスが外れたタイミングで更新（入力のたびに通信しない）   |
| `wire:model.defer` | フォーム送信時など、明示的なアクションまで同期しない（おすすめ） |
| `wire:model.live`  | 常にリアルタイム反映（デフォルト）                |
<br><br>

## wire:click：クリック時にメソッド実行
resources/views/livewire/component-name.php  
`<button wire:click="store">作成</button>`  
<br>

App/Http/Livewire/ComponentName.php
```
public function store()
{
    // バリデーション＆保存
}
```
* クリックにより、Livewireクラスの中のメソッドが呼ばれる

### 引数を渡す場合
`<button wire:click="delete({{ $task->id }})">削除</button>`

```
public function delete($id)
{
    Task::find($id)->delete();
}
```
<br><br>

## wire:key：要素の一意識別子（ループ内での再描画最適化）
resources/views/livewire/component-name.php
```
@foreach ($tasks as $task)
    <li wire:key="task-{{ $task->id }}">{{ $task->title }}</li>
@endforeach
```
* 再描画時に Livewire が「どの行がどのタスクか」を判別できる
* DOMの再構築が最小限になる（パフォーマンス最適化）
※無いと「順番が入れ替わったときに意図せぬ再描画」が起こる場合あり。
<br><br>

## wire:model="title" vs name="title" の違いまとめ
| 比較項目         | name属性               | wire:model属性                  |
| ------------ | -------------------- | ----------------------------- |
| 値の送信先        | フォームsubmit時にサーバーへ    | 入力の瞬間にLivewireのPHP変数へ反映       |
| 双方向更新        | ❌ 一方通行（input → サーバー） | ✅ 双方向（input ⇄ Livewire変数）     |
| JS不要         | ⚠️ submit必要          | ✅ 自動で通信                       |
| バリデーションタイミング | Controllerで処理後       | Livewire内でリアルタイム or メソッド呼び出し時 |
