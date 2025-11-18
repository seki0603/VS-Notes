# 既存 MVC にリアルタイムで動作する一部の MVC として Livewire を使用する

## 前提

- Laravel プロジェクトは既に構築済み（Docker や Sail など環境構築完了）
- テーブル設計・モデル・リレーション作成済み
- 認証機能や DB 接続など、通常 MVC の初期設定が済んでいる
- Livewire を導入するのは「特定のページの一部（UI 操作が多い箇所）」
- 導入するページの表示と Livewire 非該当機能について MVC 実装が終わっている
  <br><br>

## インストールとセットアップ

1. インストール...`composer require livewire/livewire`
2. コンポーネント作成...`php artisan make:livewire ComponentName`
   - app/Livewire/ComponentName.php と resources/views/livewire/component-name.blade.php が自動生成
3. 埋め込み先の blade の,head タグ内に`@livewireStyles`、body の閉じタグ直前に`@livewireScripts`を追記
4. Livewire を埋め込みたい部分に`<livewire:component-name />` or `@livewire('component-name')`記述
   <br><br>

## 動作確認

### 手順

1. ブラウザで Livewire を埋め込んだページを開く
2. DevTools → Network タブ を開く
3. ページを再読み込み（Ctrl + R）
4. フィルタ欄で livewire などと検索
   <br>

### 正常状態のサイン

| 種類               | 例                                  | 意味                                                          |
| ------------------ | ----------------------------------- | ------------------------------------------------------------- |
| **JS リソース**    | `/livewire/livewire.js?id=xxxxxxxx` | Livewire のクライアントスクリプト（JS）が読み込まれている     |
| **XHR リクエスト** | `/livewire/update?...`              | Livewire がバックエンドと通信している（ボタン押下などで発生） |

<br>

### エラー例

| 現象                           | 原因                                                                      |
| ------------------------------ | ------------------------------------------------------------------------- |
| `livewire.js` が 404           | `@livewireScripts` が `<body>` 直前に入っていない or JS がキャッシュ破損  |
| Livewire 通信が 419（CSRF）    | `<head>` に `@livewireStyles` がない or `<meta name="csrf-token">` が欠落 |
| Livewire 通信が 500            | Livewire コンポーネント内の PHP 例外                                      |
| 画面上に “Component not found” | `<livewire:xxx>` の名前が実ファイル名と不一致                             |

<br>

## 実装開始

### 内部構造

```
Controller（ページ全体制御）
 └── index.blade.php（親ビュー）
       └── <livewire:article-list /> ← 小さなMVCをここに埋め込む
             ├── app/Livewire/ArticleList.php（動作ロジック）
             └── views/livewire/article-list.blade.php（UI）
```

<br>

### 注意点

Livewire コンポーネントのルート要素は一つでなければならない。  
（一つの div タグの中に表示する他のタグ全てが収まってなければいけない）

```
<!-- ✅ OK：1つのルートdivにまとめる -->
<div>
    // 他の必要なタグは全てこの中
</div>
```

### 応用実装

| 目的                                          | 方法                                                 |
| --------------------------------------------- | ---------------------------------------------------- |
| ✅ FormRequest を利用してバリデーションを統一 | `$this->validate((new XxxRequest())->rules())`       |
| ✅ モデル操作（DB CRUD）                      | Livewire クラス内で Eloquent 操作 OK                 |
| ✅ セッションとの連携                         | `session()->put()`, `session()->get()` で通常通り    |
| ✅ イベント通信（親 → 子, 子 → 親）           | `$this->emit('updated')`, `$listeners = ['updated']` |
| ✅ SPA 的なページ遷移                         | `<a wire:navigate href="/next">`                     |

<br>

### テスト・デバッグ・リファクタ

| 項目               | 方法                                                           |
| ------------------ | -------------------------------------------------------------- |
| 🧪 Livewire テスト | Laravel 標準テストに `Livewire::test(Component::class)` を利用 |
| 🧹 Blade 整形      | 動的部分は `wire:key` を適切に付与して再レンダー最適化         |
| 🧠 メソッド分割    | `$this->reset()` / `$this->resetValidation()` でリセット整理   |
| ⚙️ パフォーマンス  | `lazy` / `defer` 修飾子で無駄な通信を減らす                    |

<br><br>
