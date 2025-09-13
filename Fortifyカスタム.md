# Fortify 認証アクションのカスタムバインド
## FortifyServiceProvider を編集

app/Providers/FortifyServiceProvider.php に以下の use 文を追加：
```
use Laravel\Fortify\Fortify;
use App\Http\Requests\RegisterRequest;
use App\Http\Requests\LoginRequest;
use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Facades\Hash;
use Illuminate\Validation\ValidationException;
use App\Models\User;
```
<br>

## boot メソッド内で Fortify にカスタムロジックを登録
### 登録処理のカスタマイズ：
```
Fortify::createUsersUsing(function (RegisterRequest $request) {
    return User::create([
        'name' => $request->name,
        'email' => $request->email,
        'password' => Hash::make($request->password),
    ]);
});
```
※ 上記の name, email, password は User モデルとフォームの仕様に合わせて調整してください。
<br>

### ログイン処理のカスタマイズ：
```
Fortify::authenticateUsing(function (LoginRequest $request) {
    $user = User::where('email', $request->email)->first();

    if (! $user || ! Hash::check($request->password, $user->password)) {
        throw ValidationException::withMessages([
            Fortify::username() => [__('auth.failed')],
        ]);
    }

    return $user;
});
```
LoginRequest 側でバリデーション済みなので $request->validated() を使ってもOKです。
<br>

## RegisterRequest / LoginRequest を Fortify に認識させる仕組み（補足）

この方法では ServiceProvider 側で直接 FormRequest を型指定しているため、Fortify の元の内部 Request（RegisterUserRequest, LoginRequest など）を置き換えるような特殊な設定は不要です。

## 最終確認：ルーティングのままでOK？

Fortify のルート（/register /login）はそのまま使えます。
変更不要です。

## つまずきポイント注意：

FortifyServiceProvider を App\Providers に正しく登録しているか確認
→ config/app.php に不要。FortifyServiceProvider は config/fortify.php に enabled があれば自動登録されます。

.env に QUEUE_CONNECTION=sync を指定しておく（メール通知が同期処理されるように）

User モデルで fillable が設定されているか確認（name, email, password など）