# Fortify カスタマイズ実装フロー

## 環境情報

| 項目         | 内容                                   |
| ------------ | -------------------------------------- |
| PHP          | 8.1.33                                 |
| Laravel      | 8.83.29                                |
| MySQL        | 8.0.26                                 |
| nginx        | 1.21.1                                 |
| 認証方式     | Laravel Fortify                        |
| メール       | MailHog                                |
| ユーザー区分 | 一般ユーザー / 管理者                  |
| FormRequest  | RegisterRequest.php / LoginRequest.php |
| メール認証   | 有効（Features::emailVerification()）  |

## 前提条件
マイグレーションファイル・モデル作成済み
Userモデル
```
use Illuminate\Contracts\Auth\MustVerifyEmail;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Foundation\Auth\User as Authenticatable;
use Illuminate\Notifications\Notifiable;
use Laravel\Sanctum\HasApiTokens;

class User extends Authenticatable implements MustVerifyEmail
{
    use HasApiTokens, HasFactory, Notifiable;

    protected $fillable = [
        'name',
        'email',
        'password',
        'role'
    ];

    protected $hidden = [
        'password',
        'remember_token',
    ];

    protected $casts = [
        'email_verified_at' => 'datetime',
    ];
}
```

## Fortify インストール

PHP コンテナ内で以下コマンド実行

```
$ composer require laravel/fortify
$ php artisan vendor:publish --provider="Laravel\Fortify\FortifyServiceProvider"
```

## Fortify 設定

### config/fortify.php を編集

#### 主な設定

- `'guard' => 'web'`（通常ユーザー用）
- `'lowercase_usernames' => true`（大文字メール対策）
- `'features'` に `registration()`・`emailVerification()` などを有効化

#### 有効機能

- registration()
- resetPasswords()
- emailVerification()

### config/app.php を編集

`'locale' => 'ja',`

```
'providers' => [
// 中略
  App\Providers\RouteServiceProvider::class,
+ App\Providers\FortifyServiceProvider::class,
]
```

### RouteServiceProvider.php を編集

`public const HOME = 'メインのリダイレクト先のパス';`

### 日本語ファイルのインストール

PHP コンテナ内で以下のコマンドを実行

```
$ composer require laravel-lang/lang:~7.0 --dev
$ cp -r ./vendor/laravel-lang/lang/src/ja ./resources/lang/
```

## 認証処理カスタマイズ

FortifyServiceProvider.php 編集

```
use App\Models\User;
use App\Http\Requests\LoginRequest;
use Illuminate\Support\Facades\Hash;
use Illuminate\Validation\ValidationException;
use Illuminate\Support\ServiceProvider;
use Laravel\Fortify\Fortify;
use Laravel\Fortify\Http\Requests\LoginRequest as FortifyLoginRequest;

public function boot(): void
    {
        $this->app->bind(FortifyLoginRequest::class, LoginRequest::class);
        Fortify::username('email');

        Fortify::authenticateUsing(function (LoginRequest $request) {
            $user = User::where('email', $request->email)->first();

            if (! $user || ! Hash::check($request->password, $user->password)) {
                throw ValidationException::withMessages([
                    Fortify::username() => [__('auth.failed')],
                ]);
            }

            return $user;
        });
    }
```

- 必要に応じて非会員ユーザーのログイン失敗メッセージをカスタマイズ
  resources/lang/ja/auth.php
  `'failed'   => 'ログイン情報が登録されていません',`

## 自作FormRequest用意
* Requests/RegisterRequest.php
* Request/LoginRequest.php
バインド設定記述
```
use Laravel\Fortify\Http\Requests\LoginRequest as FortifyLoginRequest;

class LoginRequest extends FortifyLoginRequest
```

## 登録機能実装

### AuthController or UserController 作成

### showRegisterForm と store アクション作成

```
use App\Models\User;
use App\Http\Requests\RegisterRequest;
use Illuminate\Http\Request;
use Laravel\Fortify\Fortify;
use Illuminate\Support\Str;
use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Facades\Hash;
use Illuminate\Auth\Events\Registered;

public function showRegisterForm()
    {
        return view('auth.register');
    }

public function store(RegisterRequest $request)
    {
        if (config('fortify.lowercase_usernames')) {
            $request->merge([
                Fortify::username() => Str::lower($request->{Fortify::username()}),
            ]);
        }

        $user = User::create([
            'name' => $request->name,
            'email' => $request->email,
            'password' => Hash::make($request->password),
            'role' => 'user',
        ]);

        Auth::login($user);
        event(new Registered($user));

        return redirect()->route('verification.notice');
    }
```

### ルーティング設定

```
// 会員登録前
Route::get('/register', [AuthController::class, 'showRegisterForm'])->name('register');
Route::post('/register/store', [AuthController::class, 'store'])->name('register.store');

// 登録後・メール認証前
Route::middleware(['auth'])->group(function () {
    Route::get('email/verify', [VerificationController::class, 'notice'])->name('verification.notice');
    Route::get('/email/verify/page', [VerificationController::class, 'page'])->name('verification.site');
    Route::get('/email/verify/{id}/{hash}', [VerificationController::class, 'verify'])
        ->middleware(['signed'])->name('verification.verify');
});

// メール認証後
Route::middleware(['auth', 'verified'])->group(function () {
    Route::get('/attendance', [AttendanceController::class, 'showForm'])->name('attendance.show_form');
});
```

### 各 blade 作成

- auth/register.blade.php

```
<form class="form" action="{{ route('register.store') }}" method="POST" novalidate>
                @csrf
                <div class="form__item">
                    <label class="form__item-label">名前</label>
                    <input class="form__item-input" type="text" name="name" value="{{ old('name') }}">
                    <div class="error">
                        @error('name')
                        {{ $message }}
                        @enderror
                    </div>
                </div>
                <div class="form__item">
                    <label class="form__item-label">メールアドレス</label>
                    <input class="form__item-input" type="email" name="email" value="{{ old('email') }}">
                    <div class="error">
                        @error('email')
                        {{ $message }}
                        @enderror
                    </div>
                </div>
                <div class="form__item">
                    <label class="form__item-label">パスワード</label>
                    <input class="form__item-input" type="password" name="password" value="{{ old('password') }}">
                    <div class="error">
                        @error('password')
                        @if ($message !== 'パスワードと一致しません')
                        {{ $message }}
                        @endif
                        @enderror
                    </div>
                </div>
                <div class="form__item">
                    <label class="form__item-label">パスワード確認</label>
                    <input class="form__item-input" type="password" name="password_confirmation"
                        value="{{ old('password_confirmation') }}">
                    <div class="error">
                        @error('password')
                        @if ($message === 'パスワードと一致しません')
                        {{ $message }}
                        @endif
                        @enderror
                    </div>
                </div>
                <button class="form__button" type="submit">登録する</button>
            </form>
```

要件に合わせてメール認証時の blade も用意

- auth/verify-email.blade.php（メール認証誘導画面）
- auth/verify-email-page.blade.php（メール認証画面）

メール再送ボタンを実装する場合

```
<form action="{{ route('verification.send') }}" method="POST">
@csrf
    <button class="content__send" type="submit">認証メールを再送する</button>
</form>
```

登録後のリダイレクト先のbladeを用意
* auth/login.blade.phpやindex.blade.phpなど

## メール認証機能実装
### .envを編集
```
MAIL_MAILER=smtp
MAIL_HOST=mailhog
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS="no-reply@example.test"
MAIL_FROM_NAME="Attendance Management App" // アプリの名前
```

### コントローラ処理実装
VerificationController作成
```
use Illuminate\Foundation\Auth\EmailVerificationRequest;

class VerificationController extends Controller
{
    public function notice()
    {
        return view('auth.verify-email');
    }

    public function page()
    {
        return view('auth.verify-email-page');
    }

    public function verify(EmailVerificationRequest $request)
    {
        $request->fulfill();

        return redirect()->route('attendance.show_form');
    }
}
```

上記までで、登録からメール認証完了後リダイレクトまでブラウザで確認可能

## ログイン機能実装
### showLoginFormアクション作成
AuthControllerまたはUserControllerに追記
```
public function showLoginForm()
    {
        return view('auth.login');
    }
```

### ルーティング設定
```
// 会員登録前
Route::get('/login', [AuthController::class, 'showLoginForm'])->name('login');
Route::post('/login', [\Laravel\Fortify\Http\Controllers\AuthenticatedSessionController::class, 'store']);
```

### blade作成
auth/login.blade.php
```
<form class="form" action="{{ route('login') }}" method="POST" novalidate>
@csrf
    <div class="form__item">
        <label class="form__item-label">メールアドレス</label>
        <input class="form__item-input" type="email" name="email" value="{{ old('email') }}">
        <div class="error">
        @error('email')
        {{ $message }}
        @enderror
        </div>
    </div>
    <div class="form__item">
        <label class="form__item-label">パスワード</label>
        <input class="form__item-input" type="password" name="password" value="{{ old('password') }}">
        <div class="error">
        @error('password')
        {{ $message }}
        @enderror
        </div>
    </div>
    <button class="form__button">ログインする</button>
</form>
```

ログイン後リダイレクト先が既に作成してある場合、ブラウザ上で確認可能。

## ログアウト機能実装
### logoutアクション作成
AuthControllerまたはUserControllerに追記
```
public function logout(Request $request)
    {

        Auth::logout();

        if ($request->hasSession()) {
            $request->session()->invalidate();
            $request->session()->regenerateToken();
        }

        return redirect()->route('login');
    }
```

### ルーティング設定
```
// 会員登録前
Route::post('/logout', [AuthController::class, 'logout'])->name('logout');
```

### blade側にログアウト機能を用意
ヘッダー内など
```
<form class="header__button" action="{{ route('logout') }}" method="POST">
@csrf
    <button class="header__button-logout" type="submit">ログアウト</button>
</form>
```

ブラウザ上で確認可能

## 管理者ログイン機能実装
### Fortifyにリダイレクト分岐を設定
FortifyServiceProvider.phpに追記
```
use App\Http\Responses\CustomLoginResponse;
use Laravel\Fortify\Contracts\LoginResponse as LoginResponseContract;

public function register()
    {
        $this->app->singleton(LoginResponseContract::class, CustomLoginResponse::class);
    }
```

### CustomLoginResponse.php作成
ディレクトリが無ければ先に作成
`mkdir app/Http/Responses`

```
<?php

namespace App\Http\Responses;

use Laravel\Fortify\Contracts\LoginResponse as LoginResponseContract;

class CustomLoginResponse implements LoginResponseContract
{
    public function toResponse($request)
    {
        $user = auth()->user();

        if ($user->role === 'admin') {
            return redirect()->intended('/admin/attendance/list');
        }

        return redirect()->intended('/attendance');
    }
}
```

### showAdminLoginFormアクション作成
AuthControllerまたはUserControllerに追記
```
public function showAdminLoginForm()
    {
        return view('admin.login');
    }
```

### ルーティング設定
```
// 会員登録前
Route::get('/admin/login', [AuthController::class, 'showAdminLoginForm'])->name('admin.login');

// 管理者ログイン後
Route::middleware(['auth', 'admin'])->prefix('admin')->group(function () {
    Route::get('attendance/list', [AdminAttendanceController::class, 'index'])->name('admin.attendance.list');
});
```

### blade作成
admin/login.blade.phpなど
```
<form class="form" action="{{ route('login') }}" method="POST" novalidate>
    @csrf
    <div class="form__item">
        <label class="form__item-label">メールアドレス</label>
        <input class="form__item-input" type="email" name="email" value="{{ old('email') }}">
        <div class="error">
            @error('email')
            {{ $message }}
            @enderror
        </div>
    </div>
    <div class="form__item">
        <label class="form__item-label">パスワード</label>
        <input class="form__item-input" type="password" name="password" value="{{ old('password') }}">
        <div class="error">
            @error('password')
            {{ $message }}
            @enderror
        </div>
    </div>
    <button class="form__button">管理者ログインする</button>
</form>
```
* リダイレクト分岐を行うため、routeは一般と共有でOK

### Middleware作成・登録
`php artisan make:middleware AdminMiddleware`  

app/Http/Middleware/AdminMiddleware.php
```
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class AdminMiddleware
{
    public function handle(Request $request, Closure $next)
    {
        if (auth()->check() && auth()->user()->role === 'admin') {
            return $next($request);
        }

        abort(403, 'このページにはアクセスできません');
    }
}
```

app/Http/Kernel.php
```
protected $routeMiddleware = [
    // ...
    'admin' => \App\Http\Middleware\AdminMiddleware::class,
];
```

ブラウザ上で確認可能

## 管理者ログアウト機能実装
### adminLogoutアクション作成
AuthControllerまたはUserControllerに追記
```
public function adminLogout(Request $request)
    {

        Auth::logout();

        if ($request->hasSession()) {
            $request->session()->invalidate();
            $request->session()->regenerateToken();
        }

        return redirect()->route('admin.login');
    }
```

### ルーティング設定
```
// 会員登録前
Route::post('/admin/logout', [AuthController::class, 'adminLogout'])->name('admin.logout');
```

### blade側にログアウト機能を用意
ヘッダー内など
```
<form class="header__button" action="{{ route('admin.logout') }}" method="POST">
@csrf
    <button class="header__button-logout" type="submit">ログアウト</button>
</form>
```

ブラウザ上で確認可能