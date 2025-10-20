---
tags:
  - Laravel基礎
---

# ミドルウェア

「ミドルウェア」＝ルーティングで指定された処理の直前やレスポンスを返す前に行われる処理。  
コントローラが一律同じ処理を行うのに対し、ミドルウェアは個別の条件に合わせて処理を分ける際や、共通の処理を記述する際に使用される。  
リクエストやレスポンスに含まれる値の暗号化にも使用される。  
<br>

## ミドルウェアの作成

PHP コンテナ内  
`php artisan make:middleware FirstMiddleware`  
app/Http/Middleware/FirstMiddleware に記述

```
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class FirstMiddleware
{
/**
* Handle an incoming request.
*
* @param  \Illuminate\Http\Request  $request
* @param  \Closure  $next
* @return mixed
*/
public function handle(Request $request, Closure $next)
{
+         $input = "ミドルウェアが書き換えてます。";
+         $request->merge(['content'=>$input]);
            return $next($request);
}
}
```

handle メソッド内に処理を記述することでミドルウェアを実行できる
<br>
|該当箇所| 説明|
|-------|------|
|第一引数 $request|	クライアントからのリクエストの内容が格納されている|
|第二引数 $next	|Closureというクラスからインスタンス化されている関数|
|戻り値 $next($request)| 実行されると、次の処理が行われる|
<br>

merge メソッドはフォームの送信などで送られる値に新たな値を追加する。  
`merge([ キー => 値 ]);`  
上記は`$request`インスタンスの content というキーに対して`$input`の値を代入している。  
<br>

## ミドルウェアの登録

ミドルウェアは作成しただけでは有効にならない  
有効化するには app/Http/kernel.php ファイルに登録する必要がある。  
<br>

ミドルウェアの登録記述  
`ミドルウエアの短縮キー => ミドルウエアのルート`  
例）
`'first' => \App\Http\Middleware\FirstMiddleware::class`  
<br>

kernel.php の下部にある`$routeMiddleware`で命名された配列の中に記述

```
        protected $routeMiddleware = [
          'auth' => \App\Http\Middleware\Authenticate::class,
          'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
      //以下省略
+         'first' => \App\Http\Middleware\FirstMiddleware::class
      ];
```

登録が完了したらルーティングに適用  
web.php

```
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\TestController;
+ use App\Http\Controllers\MiddlewareController;

Route::get('/', [TestController::class, 'index']);
Route::post('/', [TestController::class, 'post']);
+ Route::get('/middleware', [MiddlewareController::class, 'index']);
+ Route::post('/middleware', [MiddlewareController::class, 'post'])->middleware('first');
```

ここまでで FirstMiddleware を post メソッドに登録できている。  
<br>

※短縮キーを使わずにそのままクラスを指定する場合

```
use App\Http\Middleware\FirstMiddleware;

Route::post('/', [MiddlewareController::class, 'post'])->middleware(FirstMiddleware::class);
```

<br>

### コントローラ作成

php コンテナ内で php artisan コマンド実行。コントローラ作成。  
MiddlewareController.php に記述

```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class MiddlewareController extends Controller
{
  public function index()
  {
    $item = [
      'content' => '自由に入力してください',
    ];
    return view('middleware', $item);
  }
  public function post(Request $request)
  {
    $content = $request->content;
    $item = [
      'content' => $content . 'と入力しましたね'
    ];
    return view('middleware', $item);
  }
}
```

<br>

### ビューの作成

middleware.blade.php を作成し、記述。

```
<!DOCTYPE html>
<html lang="ja">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>COACHTECH</title>
</head>

<body>
  <h1>{{$content}}</h1>
  <form action="/middleware" method="POST">
    @csrf
    <input type="text" name="content">
    <input type="submit">
  </form>
</body>

</html>
```

<br>

http://localhost/middleware にアクセスして確認  
フォームに好きな文字を入力して送信すると、「ミドルウェアが書き換えています」に置き換わる。  
フォームの情報をコントローラで処理する前に、ミドルウェアが値を置き換えている。  
<br>

## グローバルミドルウェア

「グローバルミドルウェア」＝全てのページで有効化されているミドルウェア  
<br>

グローバルミドルウェアを指定するには、kernel.php の`$middleware`を下記のように編集。

```
protected $middleware = [
  // \App\Http\Middleware\TrustHosts::class,
  \App\Http\Middleware\TrustProxies::class,
  \Fruitcake\Cors\HandleCors::class,
  \App\Http\Middleware\PreventRequestsDuringMaintenance::class,
  \Illuminate\Foundation\Http\Middleware\ValidatePostSize::class,
  \App\Http\Middleware\TrimStrings::class,
  \Illuminate\Foundation\Http\Middleware\ConvertEmptyStringsToNull::class,
+ \App\Http\Middleware\FirstMiddleware::class
];
```

FirstMiddleware がグローバルミドルウェアとして登録される。  
ルーティングでの呼び出しが不要になる為、web.php を修正。

```
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\TestController;
use App\Http\Controllers\MiddlewareController;

Route::get('/', [TestController::class, 'index']);
Route::post('/', [TestController::class, 'post']);
Route::get('/middleware', [MiddlewareController::class, 'index']);
- Route::post('/middleware', [MiddlewareController::class, 'post'])->middleware('first');
+ Route::post('/middleware', [MiddlewareController::class, 'post']);
```

http://localhost/middleware にアクセスし、先ほどと同じ実行結果になれば成功。

<br><br>

# セッション

「セッション」＝ Web サイトにアクセスしてから Web ブラウザを閉じるまでの一連の行動  
セッションの活用により、様々な値をサーバに保存しておくことが出来る。  
<br>
セッションに似た概念でクッキーというものがある  
|用語| 違い|
|---|------|
|セッション（Session）| サーバ側で値を保存する|
|クッキー（Cookie） |クライアント側で値を保存する|
<br>
クッキーを利用することでログインの手間が省けるなどのメリットがある。  
共有の PC では同じブラウザを利用することで、なりすましや個人情報の流出の恐れがあるなど、デメリットもある。  
<br>
セッションはサーバ側で値を保持するため、セキュアに扱うことが出来る。  
「セキュア」＝安全性が確保された状態  
<br>

## セッションの処理

一回のセッションで行われる処理

1. セッション ID を発行する
2. セッションファイルにセッション ID を保存する
3. クッキーにセッション ID の付与を行う
   <br>
   2 回目以降同じ Web ページにアクセスしたとき、クッキーに保存されているセッション ID とセッションファイルに保存されている ID が一致するかどうかで、ユーザーの特定をしている。  
   <br>

## セッションの使用方法

1. 値にキーという名前をつけて保存する
2. キーを指定して値を取得する
   <br>

- 値にキーという名前を付けて保存する
  `Request->session()->put(キー, 値);`  
  <br>

- キーを指定して値を取得する
  `$変数 = Request->session()->get(キー);`  
  取得して変数に代入  
  <br>

## セッションの利用

SessionController を作成・記述し、アクションを作成

```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class SessionController extends Controller
{
  public function getSes(Request $request)
  {
    $data = $request->session()->get('txt');
    return view('/session', ['data' => $data]);
  }
  public function postSes(Request $request)
  {
    $txt = $request->input('txt');
    $request->session()->put('txt', $txt);
    return redirect('/session');
  }
}
```

SessionController の処理の流れ

1. ビューで値を入力し、送信する（post）
2. postSes アクションが起動し、`session()->put()`で値を保存
3. /session にリダイレクト
4. getSes アクションが起動し、`session()->get()`で値を取得
   <br>

画面表示のテンプレート作成
resources/views/session.blade.php を作成し、記述。

```
@extends('layouts.default')
<style>
  p {
    background-color: #289ADC;
    color: white;
    padding: 5px 10px;
    width: 500px;
  }
</style>
@section('title', 'session.blade.php')

@section('content')
<p>セッションに保存した値:{{ $data }}</p>
<form action="/session" method="post">
  @csrf
  <input type="text" name="input">
  <button>送信</button>
</form>
@endsection
```

上記でフォームを送信すると postSes アクションが起動する
<br>

ルーティングを記述

```
<?php

use Illuminate\Support\Facades\Route;//上部に追記

use App\Http\Controllers\SessionController;

Route::get('/session', [SessionController::class, 'getSes']);
Route::post('/session', [SessionController::class, 'postSes']);
```

/session にアクセスして、入力欄から値を記述して送信すると、セッションに値が保存され、リロードしても値が保存されるようになる。  
<br>

## データベースの利用

これまでのやり方では、セッション情報が storage/framework/sessions にファイルで保存される。  
各セッションごとに異なるファイルを扱うため、ファイル数は増えると速度低下につながる。  
そのため、データベースを使ってセッションを利用する。
<br>

### session.php について

セッションに関する設定情報は config/session.php に用意されている。  
セッションをデータベースに変更する場合、driver の項目を以下のように変更。  
`'driver' => env('SESSION_DRIVER', 'database'),`

.env ファイルの SESSION_DRIVER を以下のように修正  
`SESSION_DRIVER=database`  
<br>
上記でデータベースを利用するようにセッションの設定が変更できる。  
.env の記述を変更する際はキャッシュをクリアしないと変更が正しく反映されない可能性がある為、以下のコマンド実行。

```
$ php artisan cache:clear
$ php artisan config:clear
$ php artisan route:clear
$ php artisan config:cache
```

<br>

最後にセッション用のマイグレーションファイル作成  
`$ php artisan session:table`  
作成されたマイグレーションファイルには、セッション用のテーブルを作成する処理が既に記述されている。  
マイグレーション実行  
`$ php artisan migrate`  
<br>
以上でデータベースを利用してセッションが動作するようになる。

<br><br>

# ページネーション

「ページネーション」＝大量のデータを同じデザインの複数のページに分割すること  
この時、ページ下部にリンクを設置することで区切った各ページに遷移することが出来る。  
<br>

## simplePaginate メソッド

`モデル::simplePaginate( 1ページに表示する数 )`  
<br>

AuthorController.php の index アクションを修正

```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\Author;

class AuthorController extends Controller
{
  public function index()
  {
    $authors = Author::simplePaginate(4);
    return view('index', ['authors' => $authors]);
  }
}
```

上記で４つずつの Author のデータを表示するようにしている
<br>

各ページネーションのリンクが表示されるように views/index.blade.php 修正

```
//略

@section('content')
<table>
  <tr>
    <th>Data</th>
  </tr>
  @foreach ($authors as $author)
  <tr>
    <td>
      {{$author->getDetail()}}
    </td>
  </tr>
  @endforeach
</table>
{{ $authors->links() }}
@endsection
```

/にアクセスすると４つだけ項目が表示される  
`$authors->links()`により`<<Previous`と`Next>>`といったリンクが表示される。  
ページネーションのリンクを生成するにはビューに以下の記述が必要  
`$変数->links()`  
<br>

## paginate メソッド

- 「simplePaginate」＝前後の移動を行うリンク
- 「paginate」＝ページ番号のリンク
  <br>

AuthorController.php の index アクションを修正

```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\Author;

class AuthorController extends Controller
{
  public function index()
  {
    $authors = Author::Paginate(4);
    return view('index', ['authors' => $authors]);
  }
}
```

<br>

ページネーションで表示される矢印が大きすぎるため、views/index.blade.php に CSS を追記

```
//略

svg.w-5.h-5 {
    /*paginateメソッドの矢印の大きさ調整のために追加*/
    width: 30px;
    height: 30px;
  }

//略
```

にアクセスすると、前後の移動の記号の間にページ番号が表示されるようになる。  
※データの数がアクションで指定した数以下（今回は４つ以下）だとページネーションは表示されない。

<br><br>

# ユーザー認証

「認証」＝ユーザーに対して個別の ID を割り振り、パスワード等で本人確認を行う仕組み。  
※Gmail や Facebook、X など、ほとんどのサービスに実装されている。  
<br>

## Laravel での認証

| 認証方法   | 概要                                                                   |
| ---------- | ---------------------------------------------------------------------- |
| Jetstream  | 高度な認証機能が必要な場合に使用する。機能が充実していてるが少々難しい |
| Fortify    | 機能面も豊富で、バックエンドのみを請け負ってくれる。                   |
| Breeze     | Jetstream の簡易版。Jetstream ほどに豊富な機能は不要な場合使用する。   |
| Laravel/ui | Jetstream や Breeze が出る前までの公式認証方法。機能が少ない。         |

<br>

## Fortify

フロントエンドに依存しないので、フロントエンドを自由に構築できる。  
その他の機能に比べて自由度が高く、柔軟に実装できる。  
<br>
ログイン、ユーザー登録、パスワードのリセット、メールの検証など、Laravel の認証機能を全て実装するために必要なルートとコントローラを作成することで使用することが出来る。  
<br>
※Fortify は Jetstream のバックエンドに使用されている。そのため、フロントエンドを自作しないなら Jetstream、自作するなら Fortify を選択する。  
<br>

## セットアップ

view のレイアウトが変わる為、新しい開発環境を作成。  
ディレクトリ名 third-laravel  
<br>

## view ファイル作成

認証機能で活用する view ファイル作成  
resources/views ディレクトリに以下ディレクトリ構成を作成し、ファイル記述。

```
resources/views
├── auth
│   ├── login.blade.php
│   └── register.blade.php
├── layouts
│   └── app.blade.php
└── index.blade.php
```

<br>

### ヘッダー部分 app.blade.php ソースコード

```
<!DOCTYPE html>
<html lang="ja">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Attendance Management</title>
  <link rel="stylesheet" href="{{ asset('css/sanitize.css') }}">
  <link rel="stylesheet" href="{{ asset('css/common.css') }}">
  @yield('css')
</head>

<body>
  <header class="header">
    <div class="header__inner">
      <div class="header-utilities">
        <a class="header__logo" href="/">
          Attendance Management
        </a>
        <nav>
          <ul class="header-nav">
            <li class="header-nav__item">
              <a class="header-nav__link" href="/mypage">マイページ</a>
            </li>
            <li class="header-nav__item">
              <form>
                <button class="header-nav__button">ログアウト</button>
              </form>
            </li>
          </ul>
        </nav>
      </div>
    </div>
  </header>

  <main>
    @yield('content')
  </main>
</body>

</html>
```

<br>

### 新規登録画面用 register.blade.php ソースコード

```
@extends('layouts.app')

@section('css')
<link rel="stylesheet" href="{{ asset('css/register.css') }}">
@endsection

@section('content')
<div class="register-form__content">
  <div class="register-form__heading">
    <h2>会員登録</h2>
  </div>
  <form class="form">
    <div class="form__group">
      <div class="form__group-title">
        <span class="form__label--item">お名前</span>
      </div>
      <div class="form__group-content">
        <div class="form__input--text">
          <input type="text" name="name" value="{{ old('name') }}" />
        </div>
        <div class="form__error">
          @error('name')
          {{ $message }}
          @enderror
        </div>
      </div>
    </div>
    <div class="form__group">
      <div class="form__group-title">
        <span class="form__label--item">メールアドレス</span>
      </div>
      <div class="form__group-content">
        <div class="form__input--text">
          <input type="email" name="email" value="{{ old('email') }}" />
        </div>
        <div class="form__error">
          @error('email')
          {{ $message }}
          @enderror
        </div>
      </div>
    </div>
    <div class="form__group">
      <div class="form__group-title">
        <span class="form__label--item">パスワード</span>
      </div>
      <div class="form__group-content">
        <div class="form__input--text">
          <input type="password" name="password" />
        </div>
        <div class="form__error">
          @error('password')
          {{ $message }}
          @enderror
        </div>
      </div>
    </div>
    <div class="form__group">
      <div class="form__group-title">
        <span class="form__label--item">確認用パスワード</span>
      </div>
      <div class="form__group-content">
        <div class="form__input--text">
          <input type="password" name="password_confirmation" />
        </div>
      </div>
    </div>
    <div class="form__button">
      <button class="form__button-submit" type="submit">登録</button>
    </div>
  </form>
  <div class="login__link">
    <a class="login__button-submit" href="/login">ログインの方はこちら</a>
  </div>
</div>
@endsection
```

<br>

### ログイン画面用 login.blade.php ソースコード

```
@extends('layouts.app')

@section('css')
<link rel="stylesheet" href="{{ asset('css/login.css') }}">
@endsection

@section('content')
<div class="login-form__content">
  <div class="login-form__heading">
    <h2>ログイン</h2>
  </div>
  <form class="form">
    <div class="form__group">
      <div class="form__group-title">
        <span class="form__label--item">メールアドレス</span>
      </div>
      <div class="form__group-content">
        <div class="form__input--text">
          <input type="email" name="email" value="{{ old('email') }}" />
        </div>
        <div class="form__error">
          @error('email')
          {{ $message }}
          @enderror
        </div>
      </div>
    </div>
    <div class="form__group">
      <div class="form__group-title">
        <span class="form__label--item">パスワード</span>
      </div>
      <div class="form__group-content">
        <div class="form__input--text">
          <input type="password" name="password" />
        </div>
        <div class="form__error">
          @error('password')
          {{ $message }}
          @enderror
        </div>
      </div>
    </div>
    <div class="form__button">
      <button class="form__button-submit" type="submit">ログイン</button>
    </div>
  </form>
  <div class="register__link">
    <a class="register__button-submit" href="/register">会員登録の方はこちら</a>
  </div>
</div>
@endsection
```

<br>

### トップページ用 index.blade.php ソースコード

```
@extends('layouts.app')

@section('css')
<link rel="stylesheet" href="{{ asset('css/index.css') }}">
@endsection

@section('content')
<div class="attendance__alert">
  // メッセージ機能
</div>

<div class="attendance__content">
  <div class="attendance__panel">
    <form class="attendance__button">
      <button class="attendance__button-submit" type="submit">勤務開始</button>
    </form>
    <form class="attendance__button">
      <button class="attendance__button-submit" type="submit">勤務終了</button>
    </form>
  </div>
  <div class="attendance-table">
    <table class="attendance-table__inner">
      <tr class="attendance-table__row">
        <th class="attendance-table__header">名前</th>
        <th class="attendance-table__header">開始時間</th>
        <th class="attendance-table__header">終了時間</th>
      </tr>
      <tr class="attendance-table__row">
        <td class="attendance-table__item">サンプル太郎</td>
        <td class="attendance-table__item">サンプル</td>
        <td class="attendance-table__item">サンプル</td>
      </tr>
    </table>
  </div>
</div>
@endsection
```

<br>

## css ファイル作成

認証機能で活用する CSS ファイルを作成  
public ディレクトリに css ディレクトリを作成し、CSS ファイル記述。

```
public/css
├── common.css
├── index.css
├── login.css
├── register.css
└── sanitize.css
```

<br>

リセット CSS（sanitize.css）記述
<br>

### ヘッダー部分 common.css

```
.header {
  background: #000;
}

.header__inner {
  margin: 0 auto;
  padding: 20px 15px;
  max-width: 1230px;
}

.header-utilities {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.header__logo {
  color: #fff;
  text-decoration: none;
  font-weight: bold;
  font-size: 24px;
}

.header-utilities nav {
  width: 18%;
}

.header-nav {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.header-nav__link {
  color: #fff;
  text-decoration: none;
}

.header-nav__button {
  border: none;
  background: transparent;
  color: #fff;
  font-size: 16px;
  cursor: pointer;
}
```

<br>

### 新規登録画面用 register.css

```
.register-form__content {
  margin: 0 auto;
  padding: 30px 15px;
  max-width: 1230px;
}

.register-form__heading {
  margin-bottom: 20px;
  text-align: center;
}

.form {
  margin: 0 auto;
  width: 25%;
  text-align: center;
}

.form__group {
  margin-bottom: 5px;
}

.form__group-title {
  text-align: left;
}

.form__error {
  height: 20px;
  color: #ff0000;
  text-align: left;
}

.form__input--text input {
  padding: 10px;
  width: 100%;
  height: 40px;
  border: 1px solid #ddd;
  border-radius: 3px;

  appearance: none;
}

.form__button {
  margin-top: 20px;
}

.form__button-submit {
  padding: 10px;
  width: 30%;
  height: 50px;
  border: none;
  border-radius: 3px;
  background: #000;
  color: #fff;
  cursor: pointer;
}

.login__link {
  margin-top: 20px;
  text-align: center;
}
```

<br>

### ログイン画面用 login.css

```
.login-form__content {
  margin: 0 auto;
  padding: 30px 15px;
  max-width: 1230px;
}

.login-form__heading {
  margin-bottom: 20px;
  text-align: center;
}

.form {
  margin: 0 auto;
  width: 25%;
  text-align: center;
}

.form__group {
  margin-bottom: 5px;
}

.form__group-title {
  text-align: left;
}

.form__error {
  height: 20px;
  color: #ff0000;
  text-align: left;
}

.form__input--text input {
  padding: 10px;
  width: 100%;
  height: 40px;
  border: 1px solid #ddd;
  border-radius: 3px;

  appearance: none;
}

.form__button {
  margin-top: 20px;
}

.form__button-submit {
  padding: 10px;
  width: 30%;
  height: 50px;
  border: none;
  border-radius: 3px;
  background: #000;
  color: #fff;
  cursor: pointer;
}

.register__link {
  margin-top: 20px;
  text-align: center;
}
```

<br>

### トップページ用 index.css

```
.attendance__alert--success {
  padding: 10px 30px;
  border-color: #badbcc;
  background-color: #d1e7dd;
  color: #0f5132;
}

.attendance__alert--danger {
  padding: 10px 0;
  border-color: #f5c2c7;
  background-color: #f8d7da;
  color: #842029;
}

.attendance__alert--danger ul {
  margin: 0;
}

.attendance__panel {
  margin: 0 auto;
  width: 70%;
}

.attendance__button {
  width: 100%;
  text-align: center;
}

.attendance__button-submit {
  padding: 5px;
  width: 30%;
  height: 50px;
  border: none;
  border-radius: 3px;
  background: #000;
  color: #fff;
  cursor: pointer;
}

.attendance__content {
  margin: 0 auto;
  padding: 30px 15px;
  max-width: 1230px;
}

.attendance-table {
  margin: 30px auto;
  width: 70%;
  text-align: center;
}

.attendance-table__inner {
  width: 100%;
}

.attendance-table__row {
  border-bottom: 1px solid #ddd;
}

.attendance-table__header {
  padding: 12px;
  width: 20%;
  font-weight: bold;
}

.attendance-table__item {
  padding: 12px;
  width: 20%;
}
```

<br>

## Fortify の導入

PHP コンテナ内で以下コマンド実行し、インストール。

```
$ composer require laravel/fortify
$ php artisan vendor:publish --provider="Laravel\Fortify\FortifyServiceProvider"
```

上記コマンドで Fortify での認証に使用するマイグレーションファイルが作成されるため、マイグレーション実行。  
`$ php artisan migrate`  
<br>

## app.php の修正

config/app.php ファイルを２点修正
<br>

### ローカルの変更

```
- 'locale' => 'en',
+ 'locale' => 'ja',
```

アプリケーションの言語設定を英語から日本語に変更
<br>

### サービスプロバイダの追加

「サービスプロバイダ」＝アプリケーションの起動処理の初めの心臓部分  
providers に Fortify を追加することで Fortify を起動出来るようにしている

```
'providers' => [
// 中略
  App\Providers\RouteServiceProvider::class,
+ App\Providers\FortifyServiceProvider::class,
]
```

<br>

## FortifyServiceProvider.php の修正

Fortify のカスタマイズを行う際は boot メソッドを使用する  
app/Providers/FortifyServiceProvider.php の以下の部分を消去

```
public function boot()
{
Fortify::createUsersUsing(CreateNewUser::class);
-         Fortify::updateUserProfileInformationUsing(UpdateUserProfileInformation::class);
-         Fortify::updateUserPasswordsUsing(UpdateUserPassword::class);
-         Fortify::resetUserPasswordsUsing(ResetUserPassword::class);

-         RateLimiter::for('login', function (Request $request) {
-             $email = (string) $request->email;

-             return Limit::perMinute(5)->by($email.$request->ip());
-         });

-         RateLimiter::for('two-factor', function (Request $request) {
-             return Limit::perMinute(5)->by($request->session()->get('login.id'));
-         });
}
```

今回使わない機能に関して削除  
以下の記述を追加

```
public function boot()
{
Fortify::createUsersUsing(CreateNewUser::class);

+     Fortify::registerView(function () {
+         return view('auth.register');
+     });

+     Fortify::loginView(function () {
+         return view('auth.login');
+     });

+     RateLimiter::for('login', function (Request $request) {
+         $email = (string) $request->email;

+         return Limit::perMinute(10)->by($email . $request->ip());
+     });
}
```

<br>
追記した内容の役割
|設定項目|	説明|
|-------|------|
|Fortify::createUsersUsing()|	新規ユーザの登録処理|
|Fortify::registerView()|	GETメソッドで/registerにアクセスしたときに表示するviewファイル|
|Fortify::loginView()|	GETメソッドで/loginにアクセスしたときに表示するviewファイル|
|RateLimiter::for()|	login処理の実行回数を1分あたり10回までに制限|
<br>

### RouteServiceProvider.php の修正

ログイン後のリダイレクト先が/になるように修正  
app/Providers/RouteServiceProvider.php の HOME を書き換える

```
- public const HOME = '/dashboard';
+ public const HOME = '/';
```

<br>

### 日本語ファイルのインストール

Fortify のエラー文を日本語で表示する
PHP コンテナから以下のコマンド実行

```
$ composer require laravel-lang/lang:~7.0 --dev
$ cp -r ./vendor/laravel-lang/lang/src/ja ./resources/lang/
```

resources/lang ディレクトリに ja というディレクトリが作成されていれば成功  
<br>

## Fortify のルーティング

Fortify に用意されたルーティング
|機能| HTTP メソッド| URL|
|----|-------------|----|
|新規登録 |POST |/register|
|ログイン| POST| /login|
|ログアウト |POST |/logout|
上記のように認証機能の作成を行うためのルーティングが用意されている  
その URL に必要な情報を送ることで認証を実装することが出来る  
<br>

## 新規登録機能の作成

必要な情報

- name（ユーザー名）
- email（メールアドレス）
- password（パスワード）
- password_confirmation（確認用のパスワード）

### form タグの修正

resources/views/auth/register.blade.php を修正
form タグ内にある input タグの name 属性は Fortify で認証されている情報と対応している  
form タグの action 属性と method 属性を指定して、上記の情報を/register に渡す

```
- <form class="form">
+ <form class="form" action="/register" method="post">
+     @csrf
// 省略
</form>
```

<br>

## ログイン機能の作成

必要な情報

- email
- password

### form タグの修正

resources/views/auth/login.blade.php を修正
新規登録と同様にログインも Fortify で設定されている情報と対応している

```
- <form class="form">
+ <form class="form" action="/login" method="post">
+     @csrf
// 省略
</form>
```

<br>

## トップページの作成

/にアクセスしたらトップページが表示されるように設定
<br>

- Controller 作成  
  PHP コンテナ内で以下コマンド実行  
  `php artisan make:controller AuthController`  
  <br>

- アクション作成  
  AuthController.php に index アクションを作成  
  index.blade.php を呼び出す処理を記述

```
public function index()
{
  return view('index');
}
```

<br>

- ルーティング設定  
  web.php を編集して AuthController の index アクションを呼び出す設定を記述

```
<?php

use Illuminate\Support\Facades\Route;
+ use App\Http\Controllers\AuthController;

+ Route::get('/', [AuthController::class, 'index']);
```

<br>

## ログアウト機能の作成

### form タグの修正

resources/views/layouts/app.blade.php 編集

```
- <form>
+ <form class="form" action="/logout" method="post">
+     @csrf
<button class="header-nav__button">ログアウト</button>
</form>
```

<br>

### ログイン状態に応じた表示の変更

ログアウトはログイン済みのユーザーにのみ提供する機能であるため、ログイン済みの場合のみ表示するように設定。  
`Auth::check()`＝ログイン状態の判定をする。ログイン済みの場合は True を返す。  
<br>

resources/views/layouts/app.blade.php を編集

```
<ul class="header-nav">
+ @if (Auth::check())
  <li class="header-nav__item">
    <a class="header-nav__link" href="/mypage">マイページ</a>
  </li>
  <li class="header-nav__item">
    <form action="/logout" method="post">
      @csrf
      <button class="header-nav__button">ログアウト</button>
    </form>
  </li>
+ @endif
</ul>
```

ログイン済み（Auth::check()が true）の場合のみ、マイページ遷移ボタンとログアウトボタンが表示されるようになる。  
<br>

## 認証ミドルウェアの作成

ログイン済みの場合のみ、トップページを表示できるように設定。  
この様な条件に合わせて処理をしたい場合はミドルウェアを利用する。  
<br>

### ミドルウェアの作成

AuthController に index アクションを呼ぶルーティングに対してミドルウェアを設定  
web.php

```
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\AuthController;

- Route::get('/', [AuthController::class, 'index']);
+ Route::middleware('auth')->group(function () {
+     Route::get('/', [AuthController::class, 'index']);
+ });
```

auth ミドルウェアが作成され、index アクションを呼び出す前に認証済みであるかをチェックするようになる。  
認証が出来ていない場合はログインページが表示される。  
<br>

### 認証ミドルウェアの仕組み

Laravel ではデフォルトでいくつかのミドルウェアが登録されている  
登録されているミドルウェアは app/Http/Kernel.php に記述されている

```
protected $routeMiddleware = [
  'auth' => \App\Http\Middleware\Authenticate::class,
  'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
  'cache.headers' => \Illuminate\Http\Middleware\SetCacheHeaders::class,
  'can' => \Illuminate\Auth\Middleware\Authorize::class,
  'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
  'password.confirm' => \Illuminate\Auth\Middleware\RequirePassword::class,
  'signed' => \Illuminate\Routing\Middleware\ValidateSignature::class,
  'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
  'verified' => \Illuminate\Auth\Middleware\EnsureEmailIsVerified::class,
];
```

上記ルーティングに関わるミドルウェアを登録している記述のうち、  
auth に登録されている記述は以下の通り。  
`'auth' => \App\Http\Middleware\Authenticate::class,`  
ミドルウェア auth を指定すると、/App/Http/Middleware/Authenticate.php のクラスを指定していることを表している。

```
<?php

namespace App\Http\Middleware;

use Illuminate\Auth\Middleware\Authenticate as Middleware;

class Authenticate extends Middleware
{
  /**
   * Get the path the user should be redirected to when they are not authenticated.
   *
   * @param  \Illuminate\Http\Request  $request
   * @return string|null
   */
  protected function redirectTo($request)
  {
    if (!$request->expectsJson()) {
      return route('login');
    }
  }
}
```

Illuminate\Auth\Middleware\Authenticate から継承されたクラスが指定されている。

1. Illuminate\Auth\Middleware\Authenticate でログインしているかどうかの判定を行う
2. ログインされてなければ、`return route('login')`で/login にリダイレクトされる。  
   <br>

### まとめ

auth のミドルウェアを使用するとログイン画面にリダイレクトされるようになっている

<br><br>

# 単体テスト

## プログラミングにおけるテストとは

そのプログラムが正しく動作するかを検証すること  
テストの種類は大きく分けて 2 種類  
|テストの種類| 説明|
|-----------|-----|
|単体テスト(ユニットテスト)| 関数やメソッドといった小さい単位のプログラムのテスト|
|結合テスト| 単体テストで検証したプログラムを組み合わせて行うテスト|
<br>
単体テストは関数などを作成するごとに行うのが一般的
<br>

## PHPUnit とは

Laravel に用意されている単体テストツール  
コマンドを打つことで、テストコードで書かれた全ての単体テストを自動で行ってくれる。  
デフォルトではアプリケーションの tests ディレクトリに Feature と Unit の２つのディレクトリが用意されている。  
|ディレクトリ名| 説明|
|------------|------|
|Feature| Controller や Routing などの機能テストを実装する|
|Unit |単体テストのテストコードを格納する|
<br>

## テストの流れ

1. database.php の編集（データベースの設定）
2. .env.testing の作成（環境変数の設定）
3. テスト用データベースの作成（マイグレーションで作成）
4. phpunit ファイルの編集（PHP のテスト用フレームワーク）
5. テストファイルの編集（テストに関する記述）
   <br>

## テスト用データベースの準備

本番用のデータベースをテストで使うのは危険なため、テスト用のデータベースを用意しておく。  
MySQL コンテナから MySQL に root ユーザーでログインして demo_test というデータベースを作成

```
$ mysql -u root -p
> CREATE DATABASE demo_test;
> SHOW DATABASES;
```

demo_test が作成されていれば成功  
<br>

### config ファイルの変更

config/database.php の mysql の配列部分をコピーして、以下に新たに mysql_test を作成  
配列の中を変更  
|項目| 変更前| 変更後|
|---|--------|------|
|'database'| env('DB_DATABASE', 'forge')| 'demo_test'|
|'username' |env('DB_USERNAME', 'forge') |'root'|
|'password'| env('DB_PASSWORD', '') |'root'|
<br>

以下を参考に変更

```
'mysql' => [
// 中略
],

+ 'mysql_test' => [
+             'driver' => 'mysql',
+             'url' => env('DATABASE_URL'),
+             'host' => env('DB_HOST', '127.0.0.1'),
+             'port' => env('DB_PORT', '3306'),
+             'database' => 'demo_test',
+             'username' => 'root',
+             'password' => 'root',
+             'unix_socket' => env('DB_SOCKET', ''),
+             'charset' => 'utf8mb4',
+             'collation' => 'utf8mb4_unicode_ci',
+             'prefix' => '',
+             'prefix_indexes' => true,
+             'strict' => true,
+             'engine' => null,
+             'options' => extension_loaded('pdo_mysql') ? array_filter([
+                 PDO::MYSQL_ATTR_SSL_CA => env('MYSQL_ATTR_SSL_CA'),
+             ]) : [],
+ ],
```

<br>

## テスト用の.env ファイル作成

PHP コンテナから.env をコピーして.env.testing ファイル作成  
`$ cp .env .env.testing`  
<br>

.env.testing ファイルの文頭部分にある APP_ENV と APP_KEY を編集

```
APP_NAME=Laravel
- APP_ENV=local
- APP_KEY=base64:vPtYQu63T1fmcyeBgEPd0fJ+jvmnzjYMaUf7d5iuB+c=
+ APP_ENV=test
+ APP_KEY=
APP_DEBUG=true
APP_URL=http://localhost
```

| 変更点  | 説明                                                  |
| ------- | ----------------------------------------------------- |
| APP_ENV | .env.testing が test 環境用のファイルであることを示す |
| APP_KEY | テスト用のキーを作成するために、一度「空」にする      |

<br>

.env.testing にデータベースの接続情報を追加

```
  DB_CONNECTION=mysql_test
  DB_HOST=mysql
  DB_PORT=3306
- DB_DATABASE=laravel_db
- DB_USERNAME=laravel_user
- DB_PASSWORD=laravel_pass
+ DB_DATABASE=demo_test
+ DB_USERNAME=root
+ DB_PASSWORD=root
```

<br>

「空」にした APP_KEY に新たなテスト用のアプリケーションキーを加えるために PHP コンテナ内で以下のコマンド実行。  
`$ php artisan key:generate --env=testing`  
<br>

キャッシュクリアしないと反映されないこともある為、以下コマンド実行。  
`$ php artisan config:clear`
<br>

マイグレーションコマンドを実行してテスト用テーブル作成
`$ php artisan migrate --env=testing`  
<br>

## phpunit の編集

テスト用データベースでテストを実行するために phpunit.xml の編集が必要  
DB_CONNECTION と DB_DATABASE を変更

```
//略

<php>
        <server name="APP_ENV" value="testing"/>
        <server name="BCRYPT_ROUNDS" value="4"/>
        <server name="CACHE_DRIVER" value="array"/>
-         <!-- <server name="DB_CONNECTION" value="sqlite"/> -->
-         <!-- <server name="DB_DATABASE" value=":memory:"/> -->
+         <server name="DB_CONNECTION" value="mysql_test"/>
+         <server name="DB_DATABASE" value="demo_test"/>
        <server name="MAIL_MAILER" value="array"/>
        <server name="QUEUE_CONNECTION" value="sync"/>
        <server name="SESSION_DRIVER" value="array"/>
        <server name="TELESCOPE_ENABLED" value="false"/>
    </php>
</phpunit>
```

| 変更点        | 説明                                                  |
| ------------- | ----------------------------------------------------- |
| DB_CONNECTION | database.php で指定した mysql_test を指定             |
| DB_DATABASE   | テスト用のデータベースとして作成した demo_test を指定 |

<br>

## テストファイル作成

単体テストのためのファイルを作成  
PHP コンテナ内で以下コマンド実行  
`$ php artisan make:test HelloTest`  
tests/Feature/HelloTest.php が作成される  
<br>

### 基本的なテスト

HelloTest.php を修正

```
<?php

namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithFaker;
use Tests\TestCase;

class HelloTest extends TestCase
{
  public function testHello()
  {
    $this->assertTrue(true);

    $arr = [];
    $this->assertEmpty($arr);

    $txt = "Hello World";
    $this->assertEquals('Hello World', $txt);

    $n = random_int(0, 100);
    $this->assertLessThan(100, $n);
  }
}
```

<br>

記述したらキャッシュクリア  
`php artisan config:clear`  
<br>

テスト実行  
`vendor/bin/phpunit tests/Feature/HelloTest.php`

成功時 output  
`OK (1 test, 4 assertion)`

OK 以外のメッセージが出た場合は記述を見直す。  
<br>

ここで呼び出した assert 〇〇メソッドが、値が正しいかどうかをチェックしている。  
メソッドの引数にチェックする値を入れて呼び出すと、値が正しいかどうかをチェックしてくれる。  
<br>

よく使用するメソッド
|メソッド| 説明|
|-------|------|
|assertTrue(値)| 値が、True かどうかを確認する|
|assertFalse(値) |値が、False かどうかを確認する|
|assertEquals(値 1, 値 2)| 値 1 と値 2 が等しければ True を返す|
|assertNotEquals(値 1, 値 2) |値 1 と値 2 が等しくなければ True を返す|
|assertLessThan(値 1, 値 2)| 値 1 より値 2 が小さいかどうかをチェックする|
|assertLessThanOrEqual(値 1, 値 2) |値 1 より値 2 が小さいもしくは同等かどうかをチェックする|
|assertGreaterThan(値 1, 値 2)| 値 1 より値 2 が大きいかどうかをチェックする|
|assertGreaterThanOrEqual(値 1, 値 2) |値 1 より値 2 が大きいもしくは同等かどうかをチェックする|
|assertEmpty(値)| 値が「空」なら True を返す|
|assertNull(値) |値が null なら True を返す|
|assertNotEmpty(値)| 値が「空でない」なら True を返す|
|assertNotNull(値) |値が null でないなら True を返す|
|assertStringStartsWith(値 1, 値 2)| 値 1 の文頭が値 2 の文字列と一致していれば True を返す|
|assertStringEndsWith(値 1, 値 2)| 値 1 の文末が値 2 の文字列と一致していれば True を返す|
<br>

## アクセスのテスト

Web ページにアクセスし、結果を確認することを想定したテストを作成。

tests/Feature/HelloTest.php を以下のように修正

```
<?php

namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithFaker;
use Tests\TestCase;

class HelloTest extends TestCase
{
  public function testHello()
  {
    $this->assertTrue(true);

-         $arr = [];
-         $this->assertEmpty($arr);

-         $txt = "Hello World";
-         $this->assertEquals('Hello World', $txt);

-         $n = random_int(0, 100);
-         $this->assertLessThan(100, $n);
+         $response = $this->get('/');
+         $response->assertStatus(200);

+         $response = $this->get('/no_route');
+         $response->assertStatus(404);
  }
}
```

`$this->get`メソッドの戻り値から assertStatus メソッドを呼び出している  
これでトップページにアクセスし、正しくアクセスできているかチェックしている。  
<br>

PHP コンテナから phpunit コマンドでテスト実行  
`vendor/bin/phpunit tests/Feature/HelloTest.php`

成功時の output  
`OK (1 test, 3 assertion)`  
<br>

## データベースのテスト

データベースに値を挿入したり、そのデータが存在するのかを確認するテスト。  
tests/Feature/HelloTest を修正

```
<?php

namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithFaker;
use Tests\TestCase;
+ use App\Models\User;

class HelloTest extends TestCase
{
+   use RefreshDatabase;

    public function testHello()
    {
-         $this->assertTrue(true);

-         $response = $this->get('/');
-         $response->assertStatus(200);

-         $response = $this->get('/no_route');
-         $response->assertStatus(404);
+         User::factory()->create([
+             'name'=>'aaa',
+             'email'=>'bbb@ccc.com',
+             'password'=>'test12345'
+         ]);
+         $this->assertDatabaseHas('users',[
+             'name'=>'aaa',
+             'email'=>'bbb@ccc.com',
+             'password'=>'test12345'
+         ]);
    }
}
```

<br>

上記のプログラムの流れ

1. create メソッドでデータを作成する
2. assertDatabaseHas メソッドで該当のデータが存在しているのかを確認
3. RefreshDatabase でデータベースをテスト前の状態に戻す  
   <br>

phpunit コマンドでテスト実行
`vendor/bin/phpunit tests/Feature/HelloTest.php`

成功時の output
`OK (1 test, 1 assertion)`

<br><br>

# ファクトリ

## ファクトリとは

「ファクトリ」＝モデルを利用してデータベースにダミーレコードを作成する仕組み  
シーディングもダミーレコードを作る際に利用されるが、登録したいデータをそのまま記述する必要がある。  
シーディングに対して、ファクトリはランダムなデータを一度に大量に作成できる。  
<br>

ファクトリでデータベースにランダムデータを登録する処理  
app/Models/Author.php に追記

```
//略

use HasFactory;

  protected $fillable = ['name', 'age', 'nationality'];

  public static $rules = array(
    'name' => 'required',
    'age' => 'integer|min:0|max:150',
    'nationality' => 'required'
  );
  public function getDetail()

//略
```

<br>

## ファクトリの作成

ファクトリで作るデータの定義を作成

例）Author モデルで Authors テーブルに値を追加する処理
`php artisan make:factory AuthorFactory`
正常に実行されると database/factories/AuthorFactory.php が作成される  
<br>

AuthorFactory.php の definition メソッドの[]の中にデータを定義  
下記のように修正

```
<?php

namespace Database\Factories;

+ use App\Models\Author;
use Illuminate\Database\Eloquent\Factories\Factory;

class AuthorFactory extends Factory
{
  /**
   * The name of the factory's corresponding model.
   *
   * @var string
   */
  protected $model = Author::class;

  /**
   * Define the model's default state.
   *
   * @return array
   */
  public function definition()
  {
    return [
-             //
+             'name' => $this->faker->name,
+             'age' => $this->faker->numberBetween(1,100),
+             'nationality' =>$this->faker->country
    ];
  }
}
```

<br>

faker メソッドで 3 つのカラムに対してランダムデータを作成するように記述している

faker メソッドの値を生成するメソッド
|メソッド名| 機能|
|---------|------|
|boolean()| 真偽値の値をランダムに返す。|
|randomNumber() |整数値をランダムに返す。|
|numberBetween(最小値,最大値)| 引数で指定された範囲内からランダムに整数を返す。|
|shuffle (配列)| 引数の配列をランダムに入れ替えて返す。|
|randomLetter() |ランダムな文字(アルファベット) を返す。|
|word()| ランダムな文字(英単語) を返す。|
|text(文字数) |引数に指定した最大文字数のテキストを生成して返す。|
|sentence()| センテンス (一文)をランダムに生成して返す。|
|emoji |絵文字をランダムに返す。|
|name()| 名前をランダムに返す。|
|country() |国名をランダムに返す。|
|state()| ステート(州) をランダムに返す。|
|city() |街の名前をランダムに返す。|
|date()| 日付(年月日) のテキストをランダムに返す。|
|safeEmail() |メールアドレスのテキストをランダムに返す。|
|password()| パスワードのテキストをランダムに返す。|
<br>

## ファクトリのシーダーへの設定

app/database/seeders/AuthorsTableSeeder.php を修正

```
<?php


namespace Database\Seeders;

use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;
use App\Models\Author;

class AuthorsTableSeeder extends Seeder
{
  /**
   * Run the database seeds.
   *
   * @return void
   */
  public function run()
  {
    Author::factory()->count(3)->create();
  }
}
```

Author モデルを利用して Eloquent からファクトリを呼び出している  
count メソッドの引数でデータを作成する個数が決定される  
<br>

## DatabaseSeeder にシーダーを設定する

シーダーを DatabaseSeeder に登録
app/database/seeders/DatabaseSeeder.php を確認・修正

```
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;

class DatabaseSeeder extends Seeder
{
  /**
   * Seed the application's database.
   *
   * @return void
   */
  public function run()
  {
    $this->call(AuthorsTableSeeder::class);
  }
}
```

シーダーの登録が出来たらシーディングを利用  
`php artisan db:seed`  
count メソッドで設定した数だけダミーデータがファクトリを利用して生成されていれば成功  
<br>

## モデルファクトリの利用

「モデルファクトリ」＝モデルとファクトリを利用してダミーデータを作成すること  
個別のシーダーを使わずにデータを作成することが出来る  
<br>

モデルファクトリでは、AuthorFactory.php と Author.php を利用して DatabaseSeeder.php に factory メソッドを登録することでダミーデータを挿入する。

Database.php のみ以下のように編集

```
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
+ use App\Models\Author;

class DatabaseSeeder extends Seeder
{
/**
* Seed the application's database.
*
* @return void
*/
  public function run()
  {
-         $this->call(AuthorsTableSeeder::class);
+         Author::factory(10)->create();
  }
}
```

シーディング実行  
`php artisan db:seed`  
10 個のデータが authors テーブルに作成されていれば成功

<br><br>

# 論理削除と物理削除

- 「物理削除」＝データベースに保存されたデータそのものを削除する
- 「論理削除」＝データそのものを削除せず、表面的に削除しているように見せる。  
  ※物理削除はデータそのものを削除しているので復元等は出来ない  
  <br>

## 論理削除

削除されたとき用のカラムを設けることで、PC のゴミ箱に追加するように、復元可能な状態にする。

- 論理削除のメリット  
  必要なときにレコードを復元したり、一部の権限の人にのみ公開したり出来る。  
  <br>

### 物理削除の場合

1. ユーザーがアカウントを消去する
2. 管理者はデータが残っていないので誰がいなくなったか把握できない
3. アカウントを消去したユーザーの履歴が全く残らないのでユーザー分析する際も不便
   <br>

### 論理削除の場合

1. ユーザーがアカウントを消去する
2. 管理者は削除済みユーザーを確認できる
3. ユーザーはアプリケーションを使用できなくなる
4. 再度使いたくなっても管理者に依頼すれば復元できる
5. 削除ユーザーのユーザー分析も容易に出来る
   <br>

## マイグレーションの準備

例として people というテーブルとモデルで論理削除を確認

モデル作成  
`php artisan make:model Person -m`  
モデル作成時、-m オプションでマイグレーションファイルも作成できる。  
<br>

people テーブル作成  
xxx_create_people_table.php の up()の中身を修正

```
public function up()
{
  Schema::create('people', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    $table->integer('age');
    $table->timestamp('created_at')->useCurrent()->nullable();
    $table->timestamp('updated_at')->useCurrent()->nullable();
    $table->softDeletes();
  });
}
```

`$table->softDeletes();`＝論理削除をするための記述  
<br>

## モデルの編集

論理削除を考慮されたテーブルに合わせて、Person モデルを編集。  
app/Models/Person.php に追記

```
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;

class Person extends Model
{
  use HasFactory;
  use SoftDeletes;
  protected $fillable = ['name', 'age'];
}
```

<br>

マイグレーション実行  
`php artisan migrate`

http://localhost:8080/ にアクセスして people テーブルが出来ていたら成功  
<br>

## 論理削除の実行

people テーブルにはデータがないため、ファクトリを使ってダミーデータを挿入。

例として id=1 のレコードを論理削除するプログラムを作成  
routes/web.php を修正

```
<?php

use Illuminate\Support\Facades\Route;
+ use App\Models\Person;

// 中略

+ Route::get('/softdelete', function () {
+     Person::find(1)->delete();
+ });
```

/softdelete にアクセスすると id=1 のレコードが削除される  
http://localhost:8080/ にアクセスし、people テーブルを確認すると、id=1 のレコードの deleted_at に削除された日時が追加される。  
<br>

## 論理削除されたレコードの確認

以下のメソッドを用いると論理削除されたレコードを取得できる  
`モデル名::onlyTrashed()->get();`  
<br>

onlyTrashed()で論理削除されたレコードを指定して、get()で取得。  
web.php 追記

```
Route::get('softdelete/get', function() {
  $person = Person::onlyTrashed()->get();
  dd($person);
});
```

softdelete/get にアクセスし、items:array をクリックすると様々なオブジェクトが確認できる。  
attributes の部分をクリックすると削除されたレコードが確認できる。  
<br>

## 削除されたレコードの復元

以下のメソッドを用いると削除されたレコードを復元できる  
`モデル名::onlyTrashed()->restore()`  
<br>

onlyTrashed()で削除されたレコードを指定して、restore で復元。  
web.php に追記

```
Route::get('softdelete/store', function() {
  $result = Person::onlyTrashed()->restore();
  echo $result;
});
```

softdelete/store にアクセスし、復元が成功すると画面に 1 が表示される。  
失敗したり、既に復元するものがないときは 0 が返ってくる。  
http://localhost:8080/ で people テーブルを確認すると、delete_at 部分が日付から NULL になっている。  
<br>

## 論理削除したレコードの完全削除

論理削除されたレコードは以下のメソッドで完全削除できる  
`Person::onlyTrashed()->forceDelete();`  
<br>

onlyTrashed で論理削除されたレコードを指定して、forceDelete で完全削除。  
web.php に追記

```
Route::get('softdelete/absolute', function() {
  $result = Person::onlyTrashed()->forceDelete();
  echo $result;
});
```

softdelete/absolute にアクセスし、完全削除が成功すると画面に１が表示される。  
失敗したり、すでに完全削除するものがない時は 0 が返ってくる。  
http://localhost:8080/ から該当のレコードを確認すると、完全に削除されていることが確認できる。

<br><br>

# UUID

「UUID」＝ 16 進法の文字列で表現される ID。Universally Unique Identifier の略で、世界中で重複しない一意の ID。  
例）550e8400-e29b-41d4-a716-446655440000
<br>

ユーザー ID が全て数値の場合、15000 という ID が発行された時点で 14999 人のユーザーがいるということが明らかになるという事態や、若い番号のユーザーばかりがサイバー攻撃で狙われる可能性がある。  
情報の機密性という意味で、重複可能性が限りなく低い UUID は数値でのみ表す ID より有効。  
Laravel でも UUID をカラムとして発行できる。
<br>

## マイグレーションファイルの作成

以下のコマンドでマイグレーションファイルとモデルを同時に作成  
`$ php artisan make:model Product --migration`  
<br>

作成された XXXX_create_products_table.php を修正

```
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateProductsTable extends Migration
{
  public function up()
  {
    Schema::create('products', function (Blueprint $table) {
      $table->id();
      $table->uuid('uuid')->unique();
      $table->string('name');
      $table->integer('price');
      $table->timestamp('created_at')->useCurrent()->nullable();
      $table->timestamp('updated_at')->useCurrent()->nullable();
    });
  }

  public function down()
  {
    Schema::dropIfExists('products');
  }
}
```

PHP コンテナからマイグレーション実行  
`php artisan migrate`  
<br>

## UUID の確認

作成した products テーブルにダミーデータを作成して UUID を確認  
先ずは products テーブル用のファクトリを作成  
`$ php artisan make:factory ProductFactory`  
<br>

database/factories/ProductFactory.php を修正

```
<?php

namespace Database\Factories;

use App\Models\Product;
use Illuminate\Database\Eloquent\Factories\Factory;

class ProductFactory extends Factory
{
  protected $model = Product::class;

  public function definition()
  {
    return [
      'name' => $this->faker->word,
      'price' => $this->faker->randomNumber,
      'uuid' => $this->faker->uuid
    ];
  }
}
```

<br>

ファクトリをシーダーに登録  
database/seeders/DatabaseSeeder.php を修正

```
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
+ use App\Models\Product;

class DatabaseSeeder extends Seeder
{
/**
* Seed the application's database.
*
* @return void
*/
  public function run()
  {
+         Product::factory(10)->create();
  }
}
```

以下のコマンドでダミーデータを作成  
`php artisan db:seed`  
http://localhost:8080/ にアクセスしてデータが出来ていれば成功  
<br>

モデルの中で ID 非表示にして UUID を表示させる  
app/Models/Product.php を修正

```
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Product extends Model
{
  protected $fillable = [
    'uuid', 'name', 'price',
  ];

  protected $hidden = [
    'id', 'created_at', 'updated_at'
  ];
  use HasFactory;
}
```

`$fillable`と`$hidden`の設定で表示・非表示の設定を切り替えることが出来る  
web.php にルーティングを追記

```
<?php

use Illuminate\Support\Facades\Route;
+ use App\Models\Product;

// 中略

+ Route::get('uuid',function() {
+     $products = Product::all();
+     foreach($products as $product){
+         echo $product.'<br>';
+     }
+ });
```

/uuid にアクセスすると Product モデルからレコードを取得し、ブラウザで表示出来るようにしている。

<br><br>

# 複数代入について

## 複数代入とは

悪意のあるユーザーがこちらの意図しないデータの保存・更新を行うように複数の項目を代入してくることを指す。  
複数代入では、悪意のあるユーザーが勝手にテーブルを操作するというセキュリティ上のリスクがあるため注意が必要。  
<br>

## データの挿入方法

Laravel でデータベースにデータを挿入する方法は以下の３つ

- fill メソッドと save メソッドの使用
- create メソッドの使用
- insert メソッド

fill、save、create メソッドは Eloquent を用いてデータ挿入を行う。  
insert メソッドは SQL を操作してデータ挿入を行う。  
<br>

## モデルとマイグレーションの作成

Pen モデルとマイグレーションファイルを作成
`php artisan make:model Pen -m`

作成された xxxx_create_pens_table.php を修正

```
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreatePensTable extends Migration
{
  /**
   * Run the migrations.
   *
   * @return void
   */
  public function up()
  {
    Schema::create('pens', function (Blueprint $table) {
      $table->id();
      $table->uuid('uuid');
      $table->string('name');
      $table->integer('price');
      $table->timestamp('created_at')->useCurrent()->nullable();
      $table->timestamp('updated_at')->useCurrent()->nullable();
    });
  }

  /**
   * Reverse the migrations.
   *
   * @return void
   */
  public function down()
  {
    Schema::dropIfExists('pens');
  }
}
```

マイグレーション実行  
`php artisan migrate`  
<br>

app/Models/Pen.php を修正

```
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Pen extends Model
{
  use HasFactory;

  protected $guarded = [
    'id'
  ];

  protected $fillable = [
    'uuid', 'name', 'price'
  ];
}
```

| プロパティ | 別名           | 説明                                         |
| ---------- | -------------- | -------------------------------------------- |
| $guarded   | ブラックリスト | 指定したカラムに対して書き換えを不可能にする |
| $fillable  | ホワイトリスト | 指定したカラムに対して書き換え可能にする     |

<br>

## コントローラの作成

PenController を作成  
`php artisan make:controller PenController`

作成した PenController.php を修正

```
<?php


namespace App\Http\Controllers;

use App\Models\Pen;
use Illuminate\Http\Request;
use Illuminate\Support\Str;

class PenController extends Controller
{
  public function fillPen()
  {
    $pen = new Pen();
    $uuid = (string)Str::uuid();
    $pen->fill([
      'uuid' =>  $uuid,
      'name' => 'FillPen',
      'price' => 1500,
    ]);
    $pen->save();
  }
  public function createPen()
  {
    $uuid = (string)Str::uuid();
    Pen::create([
      'uuid' =>  $uuid,
      'name' => 'CreatePen',
      'price' => 1200,
    ]);
  }
  public function insertPen()
  {
    $pen = new Pen();
    $uuid = (string)Str::uuid();
    $pen::insert([
      'uuid' =>  $uuid,
      'name' => 'InsertPen',
      'price' => 1800,
    ]);
  }
}
```

web.php にルーティングを設定

```
<?php

use Illuminate\Support\Facades\Route;
+ use App\Http\Controllers\PenController;

+ Route::get('fill', [PenController::class,'fillPen']);
+ Route::get('create', [PenController::class,'createPen']);
+ Route::get('insert', [PenController::class,'insertPen']);
```

| URL     | 概要                       |
| ------- | -------------------------- |
| /fill   | fillPen アクションの実行   |
| /create | createPen アクションの実行 |
| /insert | insertPen アクションの実行 |

<br>

## 各アクションの確認

### fill メソッドと save メソッド

http://localhost/fill にアクセスし、http://localhost:8080/ を確認  
以下のデータが挿入されていれば成功  
|id| name| price|
|--|------|------|
|1 |FillPen |1500|
<br>

fillPen アクションに id=20 を指定し、追加するように PenController.php を修正

```
  public function fillPen()
  {
    $pen = new Pen();
    $uuid = (string)Str::uuid();
    $pen->fill([
+             'id' => 20,
      'uuid' =>  $uuid,
      'name' => 'FillPen',
      'price' => 1500,
    ]);
    $pen->save();
  }
```

修正後、http://localhost/fill にアクセスすると、id=20 ではなく id=2 のデータが追加される。  
`$guarded`プロパティによって id カラムの書き換えが不可能となったため、記述に関係なく自動で値が割り振られる。  
<br>

### create メソッドの使用

http://localhost/create にアクセスし、http://localhost:8080/ を確認。  
以下のデータが挿入されていれば成功  
|id| name| price|
|--|------|------|
|3| CreatePen| 1200|
<br>

createPen アクションに id=20 を指定し、追加するように PenController.php を修正。

```
  public function createPen()
  {
    $uuid = (string)Str::uuid();
    Pen::create([
+           'id' => 20,
      'uuid' =>  $uuid,
      'name' => 'CreatePen',
      'price' => 1200,
    ])
```

再度 http://localhost/create にアクセスすると id=4 のデータが挿入される。  
fill 同様、`$guarded`プロパティにより指定に関係ない id が自動で挿入される。  
<br>

### insert メソッドの使用

http://localhost/insert にアクセスし、http://localhost:8080/ を確認。  
以下のようなデータが挿入されていれば成功。  
|id| name| price|
|--|------|------|
|5 |InsertPen |1800|
<br>

insertPen アクションに id=20 を指定し、追加するよう PenController を修正。

```
  public function insertPen()
  {
    $pen = new Pen();
    $uuid = (string)Str::uuid();
    $pen::insert([
+             'id' => 20,
      'uuid' =>  $uuid,
      'name' => 'InsertPen',
      'price' => 1800,
    ]);
```

再度 http://localhost/insert にアクセスすると、Eloquent で挿入するデータと違い、id=20 のデータが挿入される。  
もう一度アクセスすると、id が重複しているため格納できないエラーが発生する。  
<br>

上記の通り、SQL で直接データを挿入する insert メソッドでは`$guarded`と`$fillable`プロパティが適用されない。

複数代入のセキュリティリスクとは insert メソッドのようにユーザー側から重要なカラムが操作できる可能性があることを指している。

そのため、Laravel では Eloquent で複数代入から保護でき、記述量を少なくできる create メソッドがよく利用されている。

<br><br>

# 日付と時間

Laravel で日付と時刻を扱う方法は大きく分けて二つ
|オブジェクト| 説明|
|-----------|------|
|DateTime オブジェクト| PHP に標準実装される日付を扱うことのできるオブジェクト|
|Carbon オブジェクト| DateTime オブジェクトを拡張し、シンプルに扱えるようにしたオブジェクト|
<br>

## DateTime

DateTime クラスを使用するためにインスタンスを生成する必要がある  
|生成方法| 説明|
|-------|------|
|new Datetime()| 現在時刻のインスタンスを生成する|
|new Datetime(value)| 引数に文字列を指定して、インスタンスを生成する|

活用例のために public//time.php を作成
<br>

## new DateTime()

現在時刻の DateTime オブジェクトのインスタンスを生成  
public/time.php に以下を記述

```
<?php
$date = new DateTime();
echo $date->format('Y-m-d H:i:s');
```

http://localhost/time.php にアクセスして確認  
以降 time.php で変更した場合は上記 URL から確認  
<br>

## new DateTime(value)

引数に文字列を指定することで DateTime オブジェクトのインスタンスを生成  
public/time.php

```
<?php
$date = new DateTime('1999-11-02 05:27:42');
echo $date->format('Y-m-d H:i:s');
```

<br>

## インスタンスから各種値の取得

```
<?php
$date = new DateTime();
echo $date->format('値');
```

<br>

| 取得する値 | 値の指定 |
| ---------- | -------- |
| 年         | Y        |
| 月         | m        |
| 日         | d        |
| 曜日       | D        |
| 時         | H        |
| 分         | i        |
| 秒         | s        |

<br>

## 出力形式

```
<?php
$date = new DateTime();
echo $date->format(DateTime::出力形式);
```

<br>

| 表示形式 | 出力                                          |
| -------- | --------------------------------------------- |
| ATOM     | 2025-08-05T19:33:13+09:00                     |
| COOKIE   | 2025 年 8 月 5 日火曜日 19 時 35 分 33 秒 JST |
| RSS      | 火曜日, 05 8 月 2025 19:36:17 +0900           |
| W3C      | 2025-08-05T19:37:04+09:00                     |

<br>

## 日付の計算

日付の加算・減算には modify メソッドを使用する。  
modify の第一引数には相対的な書式(yesterday,tomorrow)や、加算・減算する数字と単位が入る。  
<br>

### １年前・１年後

```
<?php
$date = new DateTime();
echo $date->modify('-1 years')->format('Y-m-d H:i:s');

$date = new DateTime();
echo $date->modify('1 years')->format('Y-m-d H:i:s');
```

<br>

### 1 ヵ月前・1 ヵ月後

```
<?php
$date = new DateTime();
echo $date->modify('-1 months')->format('Y-m-d H:i:s');

$date = new DateTime();
echo $date->modify('1 months')->format('Y-m-d H:i:s');
```

<br>

### 1 日前・1 日後

```
<?php
$date = new DateTime();
echo $date->modify('-1 days')->format('Y-m-d H:i:s');

$date = new DateTime();
echo $date->modify('1 days')->format('Y-m-d H:i:s');
```

<br>

### 1 時間前・1 時間後

```
<?php
$date = new DateTime();
echo $date->modify('-1 hours')->format('Y-m-d H:i:s');

$date = new DateTime();
echo $date->modify('1 hours')->format('Y-m-d H:i:s');
```

<br>

### 1 分前・1 分後

```
<?php
$date = new DateTime();
echo $date->modify('-1 minutes')->format('Y-m-d H:i:s');

$date = new DateTime();
echo $date->modify('1 minutes')->format('Y-m-d H:i:s');
```

<br>

### 1 秒前・1 秒後

```
<?php
$date = new DateTime();
echo $date->modify('-1 seconds')->format('Y-m-d H:i:s');

$date = new DateTime();
echo $date->modify('1 seconds')->format('Y-m-d H:i:s');
```

<br>

## Carbon

PHP に標準実装されている DateTime オブジェクトを拡張したパッケージ  
<br>

## セットアップ

Laravel で Carbon を使用するために composer.json を以下の通り修正

```
"require": {
上記省略
-         "laravel/tinker": "^2.5"
+         "laravel/tinker": "^2.5",
+         "nesbot/carbon": "^2.31"
},
```

記述後、PHP コンテナで以下コマンド実行。  
`composer update`

設定を反映させるため、VSCode を再起動。  
再起動後、public/carbon.php を作成。  
以下、記述。

```
<?php
require '../vendor/autoload.php';

use Carbon\Carbon;
```

以降、use 文以下に記述を追加していく。
<br>

carbon.php に追記

```
$dt = Carbon::now();
echo $dt->year;
```

http://localhost/carbon.php にアクセスし、今年の西暦が表示されていれば成功。
<br>

| 値の種類 | 表示の記述          |
| -------- | ------------------- |
| 年       | `echo $dt->year;`   |
| 月       | `echo $dt->month;`  |
| 日       | `echo $dt->day;`    |
| 時       | `echo $dt->hour;`   |
| 分       | `echo $dt->minute;` |
| 秒       | `echo $dt->second;` |

<br>

## 日付の加算・減算

日付の加算・減算には add・sub メソッドを利用する。  
<br>

```
$dt = Carbon::now();
echo $dt->add単位();

$dt = Carbon::now();
echo $dt->sub単位();
```

| 値の種類 | 単位    |
| -------- | ------- |
| 年       | Year    |
| 月       | Month   |
| 日       | Day     |
| 時       | Hour    |
| 分       | Minute  |
| 秒       | Seconds |

<br><br>

# セキュリティ対策
## セキュリティ対策とは
主にシステムの「脆弱性」を狙った攻撃を防ぐための策  
セキュリティ対策を行わないと、ウィルスに感染したり情報漏洩を引き起こす。  
<br>

代表的な対策
* CSRF対策
* XSS対策
* SQLインジェクション対策
<br>

## CSRF対策
「CSRF」＝クロスサイト・リクエスト・フォージェリ  
罠サイト等から他人のブラウザのCookieに書かれているセッションIDを取得し、そのIDなどで特定のWebアプリケーションへアクセスすることで、元のセッションのデータの持ち主がリクエストしたように偽装すること。  
<br>

Laravelではフォームに`@csrf`と記述することで対策を行うことが出来る。  
<br>

## XSS対策
「XSS」＝クロスサイトスクリプティング  
攻撃対象のWebサイトの脆弱性を突き、攻撃者がそこに悪質なサイトへ誘導するスクリプトを仕掛けることで、サイトに訪れたユーザーの個人情報などを詐取する攻撃のこと。  
<br>

原因
ビューに出力するときにエスケープ処理が出来ていない。  
エスケープをしないと、入力内容にHTMLやJavaScriptが含まれていた場合、そのままコードとして受け取り、処理を実行してしまう。  
<br>

エスケープするとコードではなく文字列として入力したものが認識される。  
Laravelではbladeの出力時に`{{}}`でエスケープすることで自動的にXSS対策を行うことが出来る。  
例）`{{ $user->user_image }}`  
<br>

## SQLインジェクション対策
SQLインジェクションとは  
SQLのデータベースに不正なプログラムを注入、挿入するサーバ攻撃の総称。  
脆弱性のあるサイトに想定とは異なる文字としてSQL文を入力して実行すると、機密情報が流出する可能性がある。  
<br>

Laravelではクエリビルダ、またはEloquentを使用することで対策を行うことが出来る。  
※DB::rawやDB::queryはSQL対策が出来ないため使用しない。  
<br>

## 注意点
基本的なフレームワークの使い方を守っていれば問題になることはほとんどないが、しっかりとセキュリティ対策をしないまま情報漏洩が起こると損害賠償を請求されることもある。  
