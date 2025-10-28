# store 実装テンプレート

## 前提

- 環境構築・DB 設計・マイグレーション・モデル作成が完了している
- 必要な場合は FormRequest の作成が終わっている
- MVC 表示機能の実装が完了している
- 表示するビューにコンポーネントを既に埋め込んでいる
  <br><br>

## livewire/component-name.blade.php

```
<form wire:submit.prevent="store" class="form" novalidate>
    <div class="form__item">
        <p class="form__title">タイトル</p>
        <input wire:model.defer="title" class="form__input" type="text">
        @error('title') <p class="error">{{ $message }}</p> @enderror
    </div>

    <div class="form__item">
        <p class="form__title">詳細</p>
        <textarea wire:model.defer="description" class="form__textarea"></textarea>
        @error('description') <p class="error">{{ $message }}</p> @enderror
    </div>

    <div class="form__item">
        <p class="form__title">期限</p>
        <input wire:model.defer="due_date" class="form__input" type="date">
        @error('due_date') <p class="error">{{ $message }}</p> @enderror
    </div>

    <div class="form__button">
        <button type="submit" class="form__button-submit">作成</button>
    </div>
</form>
```

- form タグに`wire:submit.prevent="store"`追加
- 入力欄のタグに`wire:model.defer="カラム名"`追加
  - defer...処理発火アクションがあるまで通信を停止
    <br><br>

## Livewire クラスに store メソッド実装

app/Http/Livewire/ComponentName.php

```
public $title, $description, $due_date;

    public function store()
    {
        // ✅ FormRequestルールとメッセージを再利用
        $validated = $this->validate(
            (new TaskRequest())->rules(),
            (new TaskRequest())->messages()
        );

        // ✅ データ保存
        Task::create([
        'title' => $validated['title'],
        'description' => $validated['description'],
        'due_date' => $validated['due_date'],
        'user_id' => auth()->id(),
    ]);

        // ✅ 入力欄をクリア
        $this->reset();

        // ✅ 成功メッセージ（任意）
        session()->flash('message', 'タスクを登録しました。');
    }
```

- モデルの読み込み必須（モデル名とクラス名が被らないようにする）
- use 文で Form Request 読み込み
- `public　$プロパティ(カラム名)`で入力欄と同期

* バリデーションを通過した値を変数に格納
  - 変数を使って create
* `$this->reset();`に到達するまで、入力値は保持される（old ヘルパ不要）
  <br><br>

## wire:model と wire:model.defer の違い

| 修飾子           | 通信タイミング                               | 使用目的                               | 代表的な利用場面                                   |
| ---------------- | -------------------------------------------- | -------------------------------------- | -------------------------------------------------- |
| **なし（即時）** | 入力のたびにサーバー通信                     | 即時反映・リアルタイム UI              | 検索フォーム、リアルタイムプレビュー、入力補完など |
| **`.lazy`**      | フォーカスが外れたタイミングで通信           | 入力途中は通信せず、確定時のみ送信     | 長文入力フォーム、プロフィール編集など             |
| **`.defer`**     | 明示的なアクション（例：送信ボタン）時に通信 | 通信コスト削減。フォーム送信時のみ反映 | 新規登録フォーム、編集フォームなど                 |

<br>

- wire:model.defer は「入力値を一時的にブラウザ側で保持」しておき、
  wire:click や wire:submit でメソッドが呼ばれたタイミングでまとめて送信する。
- そのため、即時検索やリアルタイムプレビューには不向き。
  そういったケースでは wire:model（即時反映）を使用する。
<br><<br>>

## まとめ

| 処理              | 内容                                       |
| ----------------- | ------------------------------------------ |
| 1️⃣ 入力           | `wire:model.defer` で値を保持（通信なし）  |
| 2️⃣ 送信           | `wire:submit.prevent="store"` で非同期通信 |
| 3️⃣ バリデーション | `$this->validate()` で FormRequest 適用    |
| 4️⃣ 保存           | `Task::create($validated)`                 |
| 5️⃣ リセット       | `$this->reset()` で入力欄クリア            |
| 6️⃣ 再描画         | Blade が自動的に再レンダーされる           |
