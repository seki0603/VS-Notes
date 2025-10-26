# 動作可能最小構成
## コマンド
```
composer create-project laravel/laravel livewire-demo
cd livewire-demo
composer require livewire/livewire
touch database/database.sqlite
php artisan migrate
php artisan serve
```

## 構成
```
livewire-demo/
├── composer.json
├── artisan
├── .env
├── app/
├── bootstrap/
├── config/
├── database/
├── public/
├── resources/
│   ├── views/
│   └── css/
├── routes/
│   └── web.php
└── storage/
```

## インストール
`composer require livewire/livewire`

# component
## 作成コマンド
`php artisan make:livewire counter`  

このコマンドにより、2つのファイルが作成される（コマンドラインに表示）
```
CLASS: app/Livewire/counter.php
VIEW:  resources/views/livewire/first-counter.blade.php
```

## blade
```
<body>
    <livewire:counter />
</body>
```

## componentファイルの役割
* app/Livewire/Counter.php...ロジックを記述する
* resources/views/livewire/counter.blade.php...ビュー

どちらも１つのコンポーネントである。  
```
Counter.php（controllerのように処理を記述）
↓
counter.blade.php(ビューの役割：ロジック側から渡されたデータの表示など)
↓
welcome.blade.phpなど(<livewire:counter />によりLivewireのbladeを読み込み)
```

# ブラウザ側でロジックを呼び出す
## Counter.phpにロジックを定義
```
public int $count = 10;

    public function changeCount() // メソッド名
    {
        $this->count = 3;
    }
```

## counter.blade.phpでロジック側のアクションを呼び出す
```
<div>
    <p>{{ $count }}</p>
    <button wire:click="changeCount">Change Count</button>
</div>
```
アクションを呼び出すきっかけにしたいタグに`wire:動作="メソッド名"`を記述。
* divタグがwire:idを取得
* ボタンクリックなどの動作により、HTTPリクエストをサーバー側に送信
* 必要なメソッドと変更を適用するビューがwire:idによって特定される。
※したがって、controllerとルーティングの記述は不要

# ユーザー側からリクエスト送信
## counter.blade.phpにモデルを呼び出す
```
<div>
    <p>{{ $count }}</p>
    <input type="number" wire:model.blur="number">
    <button wire:click="changeCount({{ $number }})">Change Count</button>
</div>
```

## Counter.phpでリクエストを受け取れるように修正
```
public int $count = 10;
    public int $number;

    public function changeCount(int $number)
    {
        $this->count = $number;
    }

    public function render()
    {
        return view('livewire.counter');
    }
```

# FormRequestの利用
| 項目              | 通常の Controller                                 | Livewire での対応方法                                                                    |
| --------------- | ---------------------------------------------- | ---------------------------------------------------------------------------------- |
| FormRequest を使う | `public function store(EntryRequest $request)` | `$this->validate((new EntryRequest())->rules(), (new EntryRequest())->messages())` |
| リクエストの取得        | `$request->input()`                            | `$this->bird_count` / `$this->notes`                                               |
| エラーメッセージの出力     | `@error('field')`                              | 同じく `@error('field')`                                                              |
| 成功後のリセット        | `return redirect()`                            | `$this->reset()` で Livewire の state 初期化                                            |

## バリデーション適用の仕組み
* 通常のLaravel（Blade + Controller）
    1. ブラウザがフォーム送信 → HTTPリクエスト
    2. バリデーションエラーでリダイレクト
    3. old() で前回の入力値を再表示
<br>

* Livewireの場合
    1. フォーム送信 → LivewireがAJAX通信でサーバーにリクエスト
    2. バリデーションエラーが返る
    3. Livewireが「コンポーネントの状態」をそのまま再レンダー

つまり、$this->bird_count や $this->notes の値は リセットされない限り保持される
→ だから old() は不要！