1. テーブル作成
2. CREATE 実装
3. バリデーション実装
4. UPDATE と DELETE 実装
   <br>

# テーブル作成

1. コントローラ作成
2. ルーティング設定
3. テーブル作成
4. Model 作成
5. 画面表示
   <br>

## コントローラ作成

PHP コンテナ内  
`php artisan make:controller コントローラ名`  
<br>

作成したコントローラに index アクションを作成

| アクション名 | 処理内容                   |
| ------------ | -------------------------- |
| index        | index.blade.php を呼び出す |

```
class TodoController extends Controller
{
+   public function index()
+   {
+     return view('index');
+   }
}
```

<br>

## ルーティング設定

web.php に記述

| URL | コントローラ   | アクション |
| --- | -------------- | ---------- |
| /   | TodoController | index      |

```
use Illuminate\Support\Facades\Route;
+ use App\Http\Controllers\TodoController;

+ Route::get('/', [TodoController::class, 'index']);
```

<br>

## テーブル作成

`php artisan make:migration create_テーブル名(複数形)_table`

マイグレーションファイル記述

```
public function up()
{
  Schema::create('todos', function (Blueprint $table) {
  $table->id();
+ $table->型('カラム名', 制約);
  $table->timestamps();
  });
}
```

<br>

マイグレーション実行  
`php artisan migrate`  
http://localhost:8080 にアクセスし、テーブルが反映されているか確認。
<br>

## Model 作成

`php artisan make:model モデル名(先頭大文字)`

作成したモデルにテーブルの操作を記述

```
class Todo extends Model
{
use HasFactory;

+     protected $fillable = ['キー'];
}
```

※キー = カラム名

アプリケーションのキーを生成  
`php artisan key:generate`  
http://localhost にアクセスし、ブラウザ表示が出来ているか確認。

<br><br>

# CREATE 実装

1. blade ファイル設定
2. 追加機能実装
3. データの表示
   <br>

## blade ファイル修正

フォームタグ修正 => ルートパスとメソッドを指定  
インプットタグ確認 => name 属性にキー名が指定してあるか確認

```
+ <form class="クラス名" action="ルートパス" method="メソッド">
+ @csrf
<!--省略-->
</form>

<input class="クラス名" type="text" name="キー名" />
```

※@csrf の付け忘れに注意！！  
<br>

## 追加機能の実装

### ルーティング設定

| HTTP メソッド | ルートパス | 実行するコントローラ | アクション |
| ------------- | ---------- | -------------------- | ---------- |
| POST          | /todos     | TodoController       | store      |

`Route::post('/todos', [TodoController::class, 'store']);`  
<br>

### コントローラ設定

- リクエストの受け取り
- データの保存
- /にリダイレクト

```
use App\Models\モデル名;

～略～

public function store(Request $request)
+     {
+         $todo = $request->only(['キー名']);
+         モデル名::create($todo);

+         return redirect('/');
+     }
```

- use 文でモデルの読み込み
- `$todo`など、キーで指定し受け取ったカラムのデータを格納する変数を決める(単数形)。
- $request->で取得方法を指定 (all, get, first など)
  <br>

実際に追加機能を実行し、http://localhost:8080/ で作成したデータが保存されているか確認。
<br>

## データの表示

### コントローラ修正

index アクションにデータベースの値を表示する処理を記述

```
public function index()
{
+         $todos = モデル名::all();

-         return view('index')
+         return view('index', compact('todos'));
}
```

- `$todos`など、モデルで取得したデータを格納する変数を定義。
- compact 関数で定義した変数`$todos`を第一引数で指定した view に送信。
  <br>

### View の修正

```
@foreach ($todos as $todo)
        <tr class="todo-table__row">
          <td class="todo-table__item">
            <form class="update-form" action="">
            @csrf
              <div class="update-form__item">
                <input class="update-form__item--input" type="text" name="キー" value="{{ $todo['キー'] }}" />
              </div>
              <div class="update-form__button">
                <button class="update-form__button--submit">更新</button>
              </div>
            </form>
          </td>
          <td class="todo-table__item">
            <form class="delete-form" action="">
              <div class="delete-form__button">
                <button class="delete-form__button--submit">削除</button>
              </div>
            </form>
          </td>
        </tr>
        @endforeach
```

@foreach ディレクティブで`$todos`の数によって表示数を変える部分を挟む。
<br>

http://localhost/ にアクセスし、データが全件表示されているか確認。
<br>

### 注意事項

- HTML フォーム `<input name="content">`
- コントローラー `$request->only(['content'])`
- モデル `$fillable = ['content']`
- マイグレーション `$table->string('content');`
  上記４つは全て同じワードに揃える => DB のカラム名とキーは同じになる。

<br><br>

# バリデーション実装

1. フラッシュメッセージの作成
2. バリデーションの実装
   <br>

## フラッシュメッセージ作成

### リダイレクト時にメッセージを送信するようにコントローラを修正

```
public function store(Request $request)
{
  $todo = $request->only(['content']);
  Todo::create($todo);

- return redirect('/');
+ return redirect('/')->with('message', 'Todoを作成しました');
}
```

<br>

`return redirect('ルート')->with('キー','メッセージ内容');`
<br>

### ビューを修正

```
@if (session('message'))
  <div class="todo__alert--success">{{ session('message') }}</div>
@endif
```

- フラッシュメッセージにする部分を`@if (session('キー'))`で挟む
- 表示内容を`{{ session('キー') }}`に変更
  <br>

ブラウザ上で CREATE を実行し、フラッシュメッセージが表示されるか確認。
<br>

## バリデーションの実装

### フォームリクエスト作成

`php artisan make:request フォーム名(アッパーキャメル)`  
<br>

作成したフォームリクエストに入力欄に対するルールを設定

```
class TodoRequest extends FormRequest
{
public function authorize()
  {
-         return false;
+         return true;
  }

  public function rules()
  {
         return [
-         //
+             'キー（カラム名）' => ['制約１', '制約２', '制約３']
         ];
  }
}
```

- authorize メソッドを false から true に変更
- rules の連想配列にキーに対する制約を記述
  <br>

### フォームリクエストにエラーメッセージを追記

```
public function messages()
  {
    return [
      'キー.制約１' => 'エラーメッセージ１',
      'キー.制約２' => 'エラーメッセージ２',
      'キー.制約３' => 'エラーメッセージ３',
    ];
  }
```

※`max:20 などの字数制約について

- rules での記述 => max:20
- messages での記述 => max
  <br>

### フォームリクエストをコントローラに反映

```
use App\Http\Requests\フォーム名(アッパーキャメル);

～略～

-     public function store(Request $request)
+     public function store(TodoRequest $request)
  {
  $todo = $request->only(['content']);
  Todo::create($todo);

  return redirect('/')->with('message', 'Todoを作成しました');
  }
```

- use 文でフォームリクエストを指定
- store アクションで受け取る Request をフォームリクエスト名に修正
  <br>

### エラーメッセージの表示

ビューを修正

```
<div class="todo__alert">
  @if(session('message'))
  <div class="todo__alert--success">
    {{ session('message') }}
  </div>
  @endif
  + @if ($errors->any())
  + <div class="todo__alert--danger">
    + <ul>
      + @foreach ($errors->all() as $error)
      + <li>{{ $error }}</li>
      + @endforeach
      + </ul>
    + </div>
  + @endif
</div>
```

- `@if ($errors->any())`でバリデーションに引っかかった際のエラーメッセージを取得
- `@foreach ($errors->all() as $error)`で全てのエラーメッセージを１件ずつに展開
- `<li>{{ $error }}</li>`で１件ずつエラーメッセージを表示

ブラウザで制約に反した入力内容を表示してエラーメッセージが表示されるか確認

<br><br>

# UPDATE と DELETE 実装

1. 更新機能実装

- 値を渡せるよう View を修正
- 対象のデータの id を取得
- id を元にデータ内容を書き換える

2. 削除機能実装

- 値を渡せるよう View を修正
- 対象のデータの id を取得する
- id を元にデータを削除する
  <br>

## 更新機能実装

| HTTP メソッド | ルートパス    | 実行するコントローラ | アクション |
| ------------- | ------------- | -------------------- | ---------- |
| PATCH         | /todos/update | TodoController       | update     |

<br>

### 値を渡せるよう View を修正

```
<form class="update-form" action="/todos/update" method="POST">
+     @method('PATCH')
+     @csrf
    <div class="update-form__item">
+     <input class="update-form__item-input" type="text" name="キー" value="{{ $todo['キー'] }}">
+     <input type="hidden" name="id" value="{{ $todo['id'] }}">
    </div>
```

- HTML の form タグに patch メソッドは指定できないため method に post を指定
- @method ディレクティブで patch メソッドを指定
- name 属性に id、value に`{{ $定義した変数['id']}}`を指定、hidden 属性を用いてコントローラに id を渡す。
  <br>

### View からデータの id を取得

ルーティングを設定  
`Route::patch('/todos/update', [TodoController::class, 'update']);`
<br>

### id を元にデータ内容を書き換える

コントローラにupdateアクションを作成

```
public function update(TodoRequest $request)
{
    $todo = $request->only(['content']);
    Todo::find($request->id)->update($todo);

    return redirect('/')->with('message', 'Todoを更新しました');
}
```

- フォームリクエスト名を指定
- `モデル::find($request->id)->update($定義した変数)`
  - リクエストされた id を元にデータを検索
  - 該当データを更新
- リダイレクト時のフラッシュメッセージを定義
  <br>

## 削除機能実装

| HTTP メソッド | ルートパス    | 実行するコントローラ | アクション |
| ------------- | ------------- | -------------------- | ---------- |
| DELETE        | /todos/delete | TodoController       | destroy    |

<br>

### 値を渡せるよう View を修正

```
+   <form class="delete-form" action="/todos/delete" method="POST">
+     @method('DELETE')
+     @csrf
    <div class="delete-form__button">
+     <input type="hidden" name="id" value="{{ $todo['id'] }}">
      <button class="delete-form__button-submit" type="submit">削除</button>
    </div>
  </form>
```

- form タグの method で post を指定
- @method ディレクティブで delete を指定
- id を input タグの hidden 属性を用いて渡す
  <br>

### 対象のデータの id を取得する
ルーティングを設定  
`Route::delete('/todos/delete', [TodoController::class, 'destroy']);`
<br>

### id を元にデータを削除する
コントローラにdestroyアクションを作成
```
public function destroy(Request $request)
{
    Todo::find($request->id)->delete();
    return redirect('/')->with('message', 'Todoを削除しました');
}
```

* `モデル::find($request->id)->delete();`
  * フォームから送信されたidを元にデータを検索
  * 該当データを削除
* リダイレクト時のフラッシュメッセージを定義
<br>

ブラウザ上で確認

<br><br>