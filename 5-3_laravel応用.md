---
tags:
  - Laravel基礎
---

# 検索とモデル結合ルート

## where による検索

ID 以外での検索に利用  
<br>

### get メソッド

複数件の取得  
`$変数 = モデルクラス::where(フィールド名 , 値)->get();`  
<br>

### first メソッド

1 件のみの取得  
`$変数 = モデルクラス::where(フィールド名 , 値)->first();`  
<br>

## name 属性を利用した検索

フォームに入力した値と一致するデータを検索する場合に利用  
<br>
resources/views 内に find.blade.php を作成して記述

```
@extends('layouts.default')
<style>
  th {
    background-color: #289ADC;
    color: white;
    padding: 5px 40px;
  }
  td {
    padding: 25px 40px;
    background-color: #EEEEEE;
    text-align: center;
  }
</style>
@section('title', 'find.blade.php')

@section('content')
<form action="find" method="POST">
@csrf
  <input type="text" name="input" value="{{$input}}">
  <input type="submit" value="見つける">
</form>
@if (@isset($item))
<table>
  <tr>
    <th>id</th>
    <th>name</th>
    <th>age</th>
    <th>nationality</th>
  </tr>
  <tr>
    <td>{{$item->id}}</td>
    <td>{{$item->name}}</td>
    <td>{{$item->age}}</td>
    <td>{{$item->nationality}}</td>
  </tr>
</table>
@endif
@endsection
```

<br>

AuthorController.php を name 属性を利用して検索できるように修正

```
<?php

namespace App\Http\Controllers;

use App\Models\Author;
use Illuminate\Http\Request;

class AuthorController extends Controller
{
    public function index()
    {
        $authors = Author::all();
        return view('index', ['authors' => $authors]);
    }
+    public function find()
+    {
+        return view('find', ['input' => '']);
+    }
+    public function search(Request $request)
+    {
+        $item = Author::where('name', 'LIKE',"%{$request->input}%")->first();
+        $param = [
+            'input' => $request->input,
+            'item' => $item
+        ];
+        return view('find', $param);
+    }
}
```

<br>

web.php に追記

```
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\AuthorController;

Route::get('/find', [AuthorController::class, 'find']);
Route::post('/find', [AuthorController::class, 'search']);
```

<br>
LIKEと%を利用すると、authorsテーブルの中で、nameが部分一致するデータのうち一つが取り出される。
<br>

name が完全一致するものだけを取り出したい場合は search アクションを下記のように記述
`$item = Author::where('name', $request->input)->first();`  
<br>

## モデル結合ルート

「暗黙の結合」＝ルーティングの際、パラメータの値と Eloquent モデルが一致すると、そのパラメータの値に一致する ID を持つモデルインスタンスを生成すること。  
<br>

使用例  
web.php に追記
`Route::get('/author/{author}', [AuthorController::class, 'bind']);`  
`{author}`の数字と Author モデルの id が合致するレコードが結びついて取り出される。  
<br>

AuthorController.php に bind アクションを記述

```
public function bind(Author $author)
    {
        $data = [
            'item'=>$author,
        ];
        return view('author.binds', $data);
    }
```

bind アクションには`Author $author`という引数が用意されている。  
これにより、パラメータ{author}の数値が Author の ID を示す値として扱われ、取り出された Author インスタンスが引数として渡される。  
<br>

ビューファイル作成
views ディレクトリに author ディレクトリ作成  
author ディレクトリに bind.blade.php を作成して記述

```
@extends('layouts.default')
<style>
th {
  background-color: #289ADC;
  color: white;
  padding: 5px 40px;
}
tr:nth-child(odd) td{
  background-color: #FFFFFF;
}
td {
  padding: 25px 40px;
  background-color: #EEEEEE;
  text-align: center;
}
</style>
@section('title', 'binds.blade.php')

@section('content')
<p>Author</p>
<table>
  <tr>
    <th>ID</th>
    <th>NAME</th>
    <th>AGE</th>
    <th>NATIONALITY</th>
  </tr>
  <tr>
    <td> {{$item->id}} </td>
    <td> {{$item->name}} </td>
    <td> {{$item->age}} </td>
    <td> {{$item->nationality}} </td>
  </tr>
</table>
@endsection
```

<br>
/author/2など、パラメータにIDをつけてアクセスすると、authorテーブルのid=2のレコードが表示される。  
<br>

### モデルにメソッドを追加

Author モデルに以下のメソッドを追加して表示方法を変更  
<br>

Author.php

```
protected $fillable = ['name', 'age', 'nationality'];

  // 追記：ここから
  public function getDetail()
  {
    $txt = 'ID:'.$this->id . ' ' . $this->name . '(' . $this->age .  '才'.') '.$this->nationality;
    return $txt;
  }
```

「getDetail()」＝ index.blade.php で利用される一つ一つのデータを加工するメソッド  
<br>

index.blade.php を修正

```
<table>
  <tr>
    <th>Data</th>
  </tr>
  @foreach ($authors as $author)
    <tr>
      <td>{{$author->getDetail()}}</td>
    </tr>
  @endforeach
</table>
```

<br><br>

# デバック

「デバック」＝プログラムが使用通り動作するか確認する作業
<br>

## dd()

指定された変数の内容を表示する  
view から送られてきたデータが、正しく Controller に送られているか確認するために使用する。  
dd 関数を使用する際、処理は停止する。

```
$item = $request→all();

dd($item);
```

request から送られてきた値を変数 item に格納し、変数 item を dd 関数で確認している。  
※`dd($request->all());`でも可能  
<br>

### 使用例

AuthorController.php を修正

```
public function index()
  {
    $authors = Author::all();
    dd($authors); // 追加
    return view('index', ['authors' => $authors]);
  }
```

<br>
localhostにアクセスし、itemsの「▶」をクリックするとAuthorsテーブルに格納しているデータを確認できる。  
<br>
確認を終えたら、AuthorController.phpのdd関数を削除。  
<br>

## tinker

「tinker」＝ laravel に搭載されているコンソール  
コンソール上で関数を実行することも出来る  
<br>

### 使い方

PHP コンテナ内で実行  
`php artisan tinker`  
`Author::all();`  
ターミナル上にデータが出力され、確認できる。  
<br>

Error Class 'Author' not found.の場合  
PHP コンテナ内に戻り、下記コマンド実行。  
`composer dumpautoload`  
<br>

全件ではなく特定のレコードを取り出す場合  
`Author::find(1);`

<br><br>

# バリデーション

「バリデーション」＝入力されたデータが規定された文法、または要求された仕様に沿って適切に記述されているか検証すること。  
不適切なデータを除外したり、クロスサイトスクリプションのような攻撃を未然に防ぐことが出来る。  
<br>

## フォームリクエスト

フォームリクエストを利用してバリデーションを実装できる

### フォームリクエスト作成

PHP コンテナ内で実行  
`php artisan make:request AuthorRequest`  
Http/Requests 内に AuthorRequest.php というファイル名で作成される。  
<br>

AuthorRequest.php のソースコードを確認

- authorize メソッド＝フォームリクエストの使用許可、不許可を返すメソッド。
- rules メソッド＝バリデーションルールを記述するメソッド
  <br>

## フォームリクエストの編集

AuthorRequest.php を修正

```
    public function authorize(){
        return true;
    }

~

    public function rules(){
        return [
            'name' => 'required',
            'age' => 'integer|min:0|max:150',
            'nationality' => 'required'
        ];
    }
```

<br>

### rules

送信された内容が各 name 属性に指定したルールに従っているか検証している  
「|」で区切ることでルールを複数適用できる

| ルール   | 求められる値    |
| -------- | --------------- |
| required | 必須            |
| integer  | 数値型          |
| email    | メール形式      |
| date     | 日付型(date 型) |
| nullabel | null 値も許容   |
| min:x    | x 以上          |
| max:y    | y 以下          |

<br>

## バリデーションの適用

作成したバリデーションを適用するにはコントローラを修正する必要がある

AuthorController.php を修正

```
use Illuminate\Http\Request;
use App\Models\Author;

~

    public function create(AuthorRequest $request){
        $form = $request->all();
        Author::create($form);
        return redirect('/');
    }
```

<br>

- use で AuthorController をインポート  
  `use App\Http\Requests\AuthorRequest;`

- create メソッドの引数を Request から AuthorRequest に変更  
  この変更により、AuthorRequest を適用できる。  
  <br>

## エラーメッセージの表示

エラーメッセージの表示はビューでの処理のみ対応できる。  
このメッセージにより、エラーを引き起こす原因が特定できる。  
<br>

resources/views/add.blade.php を修正

```
//略

@section('content')
@if (count($errors) > 0)
<ul>
    @foreach ($errors->all() as $error)
    <li>{{$error}}</li>
    @endforeach
</ul>
@endif
<form action="/add" method="post">

//略
```

@if で変数`$errors`の要素数を調べ、0 より大きい場合にエラーメッセージを表示する。  
`$errors`は Laravel で用意された、エラーメッセージを格納する変数。  
@foreach でエラーメッセージをリスト表示する。  
一つでもエラーがあると、`$errors->all()`に格納され、元の画面にリダイレクトする。  
<br>

## 入力欄ごとにエラーメッセージを表示

add.blade.php を修正

```
//略

@section('content')
@if (count($errors) > 0)
<p>入力に問題があります</p>
@endif
<form action="/add" method="post">
    <table>
    @csrf
        @if ($errors->has('name'))
        <tr>
            <th style="background-color: red">ERROR</th>
            <td>
                {{$errors->first('name')}}
            </td>
        </tr>
        @endif
        <tr>
            <th>name</th>
            <td><input type="text" name="name"></td>
        </tr>

//略
```

<br>
下記を各行の上に追加
```
@if ($errors->has('キー'))
    <tr>
        <th style="background-color: red">ERROR</th>
        <td>
            {{$errors->first('キー')}}
        </td>
    </tr>
@endif
```
<br>

@if で`$errors`にキーの属性があるかチェックしている。  
エラーがある場合は`$errors->first('キー')`という値を出力  
first メソッドは、指定した項目の最初の項目を取得する。  
エラーメッセージを複数取得する場合は get メソッドを使う  
<br>

## @error ディレクティブ

`$errors`よりも簡単にエラーメッセージを表示できる

add.blade.php を修正

```
//略

@section('content')
@if (count($errors) > 0)
<p>入力に問題があります</p>
@endif
<form action="/add" method="post">
    <table>
    @csrf
        @error('name')
        <tr>
            <th style="background-color: red">ERROR</th>
            <td>
                {{$errors->first('name')}}
            </td>
        </tr>
    @enderror

//略
```

@error ディレクティブは引数に渡した項目ごとにエラーのチェックをし、エラーがあれば`$message`という変数に渡している。
<br>

## メッセージの日本語化

AuthorRequest.php を修正

```
//略

public function rules()
  {
    return [
      'name' => 'required',
      'age' => 'integer|min:0|max:150',
      'nationality' => 'required'
    ];
  }

  public function messages()
  {
    return [
      'name.required' => '名前を入力してください',
      'age.integer' => '数値を入力してください',
      'age.min' => '0以上の数値を入力してください',
      'age.max' => '150以下の数値を入力してください',
      'nationality.required' => '国籍を入力してください',
    ];
  }
```

messages メソッドの中では対応するメッセージを自由に変えることが出来る  
`inputのname属性.バリデーションルール => '表示したいエラーメッセージ'`

<br><br>

## エラー時のリダイレクト先変更

### フォームリクエストでリダイレクト先指定

AuthorRequest.php を修正

```
//略

public function messages()
  {
    return [
      'name.required' => '名前を入力してください',
      'age.integer' => '数値を入力してください',
      'age.min' => '0以上の数値を入力してください',
      'age.max' => '150以下の数値を入力してください',
      'nationality.required' => '国籍を入力してください',
    ];
  }

  protected function getRedirectUrl()
  {
    return 'verror';
  }
```

getRedirectUlr で、バリエーション失敗時のルーティングを指定することが出来る。
return に指定したリダイレクト先に対応するルーティングとビューを作成し、コントローラとアクションを指定する。  
<br>

web.php に追記  
`Route::get('/verror', [AuthorController::class, 'verror']);`  
<br>

AuthorController.php に追記

```
//略

  public function verror()
    {
    return view('verror');
    }
```

AuthorController の verror アクションを利用
<br>

verror のビューとして resources/views 内に verror.blade.php を作成  
内容記述

```
@extends('layouts.default')

@section('title', 'verror.blade.php')

@section('content')
<body>
  <p>入力内容にエラーがありました</p>
</body>
</html>
@endsection
```

<br>

## その他の検証ルール

参照ドキュメント  
https://readouble.com/laravel/8.x/ja/validation.html

<br><br>

# モデルのリレーションの基本

## モデルのリレーションとは

テーブル同士でのデータの取得が簡単に出来るようになる機能

- 主テーブル：authors テーブル
- 従テーブル：books テーブル
  <br>

## Books テーブルと Authors テーブルのリレーション

リレーションの確認の為に books テーブル作成

- 「authors テーブル」＝リレーション元
- 「books テーブル」＝リレーション先
  <br>

books テーブル
|カラム名| 内容|
|-------|------|
|id| レコードに割り振られる ID|
|author_id| 関連する authors テーブルの ID|
|title |本のタイトル|
|created_at| 制作日時|
|updated_at |更新日時|
<br>

## マイグレーションファイルの作成

PHP コンテナからファイル名 create_books_table のマイグレーションファイル作成  
`php artisan make:migration create_books_table`  
<br>

xxxx_create_books_table.php に記述

```
//略

    public function up()
    {
        Schema::create('books', function (Blueprint $table) {
            $table->id();
            $table->integer('author_id');
            $table->string('title');
            $table->timestamp('created_at')->useCurrent()->nullable();
            $table->timestamp('updated_at')->useCurrent()->nullable();
        });
    }

//略
```

up メソッドでリレーション元の id カラムを指定する必要がある。  
`$table->integer('author_id');`  
上記記述により、authors テーブルと books テーブルが自動で結びつく。  
<br>

PHP コンテナからマイグレート実行  
`$ php artisan migrate`  
<br>

## モデル作成

`$ php artisan make:model Book`  
<br>

## Book モデル記述

app/Models/Book.php に記述

```
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Book extends Model
{
    protected $guarded = array('id');
    public static $rules = array(
        'author_id' => 'required',
        'title' => 'required',
    );

    public function getTitle(){
        return 'ID'.$this->id . ':' . $this->title;
    }
}
```

バリデーション記述と Books テーブルの情報を返す getTitle メソッドを用意  
<br>

## BookController の作成

PHP コンテナからコントローラ作成
`$ php artisan make:controller BookController`  
<br>

app/Http/Controllers/BookController.php に記述  
データの一覧表示のための index と新規投稿のための add/create メソッドを用意

```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\Book;

class BookController extends Controller
{
    public function index(){
        $items = Book::all();
        return view('book.index', ['items'=>$items]);
    }
    public function add(){
        return view('book.add');
    }
    public function create(Request $request){
        $this->validate($request, Book::$rules);
        $form = $request->all();
        Book::create($form);
        return redirect('/book');
    }
}
```

use 文で Book モデルをインポート  
index アクションでは Book:all で全レコードを呼び出し、$items という変数に渡している。  
add アクションでフォーム用のページに遷移するように設定  
create アクションで送信された値を Books テーブルに追加  
<br>

## ビューファイルの作成

resources/views 内に book ディレクトリ作成  
book ディレクトリ内に index.blade.php と add.blade.php を作成  
<br>

index.blade.php 記述

```
@extends('layouts.default')
<style>
th {
  background-color: #289ADC;
  color: white;
  padding: 5px 40px;
}
tr:nth-child(odd) td{
  background-color: #FFFFFF;
}
td {
  padding: 25px 40px;
    background-color: #EEEEEE;
  text-align: center;
}
</style>
@section('title', 'book.index.blade.php')

@section('content')
<table>
  <tr>
    <th>Books</th>
  </tr>
  @foreach ($items as $item)
  <tr>
    <td>
      {{$item->getTitle()}}
    </td>
  </tr>
  @endforeach
</table>
@endsection
```

@foreach で$itemsから順にbookレコードを取り出して表示  
本のタイトル表示はbook.phpのgetTitle()を利用  
`{{$item->getTitle()}}`  
<br>

add.blade.php に本の情報追加のためのフォームを用意

```
@extends('layouts.default')
<style>
th {
  background-color: #289ADC;
  color: white;
  padding: 5px 40px;
}

td {
  padding: 25px 40px;
  background-color: #EEEEEE;
  text-align: center;
}

button {
  padding: 10px 20px;
  background-color: #289ADC;
  border: none;
  color: white;
}
</style>
@section('title', 'book.add.blade.php')

@section('content')
@if (count($errors) > 0)
<ul>
  @foreach ($errors->all() as $error)
  <li>
    {{$error}}
  </li>
  @endforeach
</ul>
@endif
<form action="/book/add" method="post">
  <table>
    @csrf
    <tr>
      <th>author_id:</th>
      <td><input type="id" name="author_id"></td>
    </tr>
    <tr>
      <th>title:</th>
      <td><input type="text" name="title"></td>
    </tr>
  </table>
  <button>送信</button>
</form>
@endsection
```

author_id、title というカラムのフォームを用意  
これらを入力して送信すると、books テーブルにレコードが追加されるようになる。  
<br>

### ルーティングの記述

BookController.php と対応するルーティングの処理を記述

web.php

```
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\AuthorController;
use App\Http\Controllers\BookController;

//略

Route::post('/delete', [AuthorController::class, 'remove']);

Route::prefix('book')->group(function () {
    Route::get('/', [BookController::class, 'index']);
    Route::get('/add', [BookController::class, 'add']);
    Route::post('/add', [BookController::class, 'create']);
});
```

リレーションを確認する準備完了  
<br>

## has One 結合

リレーションの種類のうち、2 つのテーブルが１対１の関係で関連づけられているもの。  
「has One」は主テーブルから、従テーブルを取得するための機能。  
主テーブル側に「has One」の記述をする。
<br>

App/Models/Author.php に追記

```
public function book(){
  return $this->hasOne('App\Models\Book');
}
```

hasOne メソッドで引数にモデル（Book.php）を指定。  
モデルから引数に指定したモデルへの関連付けを行っているため、Books テーブルのレコードが取り出せるようになる。  
hasOne は１対１の関係で１つのレコードしか取り出されないため、単数形の」名前にしている。  
<br>

## リレーション用のビューファイル

リレーションによって取り出された関連テーブルを表示するビューファイル作成  
resources/views/author 内に index.blade.php 作成

```
@extends('layouts.default')
<style>
th {
  background-color: #289ADC;
  color: white;
  padding: 5px 40px;
}

tr:nth-child(odd) td {
  background-color: #FFFFFF;
}

td {
  padding: 25px 40px;
  background-color: #EEEEEE;
  text-align: center;
}
</style>
@section('title', 'author.index.blade.php')

@section('content')
<table>
  <tr>
    <th>Author</th>
    <th>Book</th>
  </tr>
  @foreach ($items as $item)
  <tr>
    <td>
      {{$item->getDetail()}}
    </td>
    <td>
      @if ($item->book != null)
      {{ $item->book->getTitle() }}
      @endif
    </td>
  </tr>
  @endforeach
</table>
@endsection
```

<br>

リレーションの確認をするために authors テーブルのデータを返すアクションが必要  
AuthorController.php の AuthorController クラスに relate アクションを追記

```
public function relate()
{
    $items = Author::all();
    return view('author.index', ['items' => $items]);
}
```

<br>

relate アクションのルーティングを設定  
web.php
`Route::get('/relation', [AuthorController::class, 'relate']);`  
<br>

- 「$item」＝ Author モデルのインスタンス
- 「インスタンス」＝クラス（設計図）を元に生成された実体（オブジェクト）  
  クラスで定義されたプロパティ（属性）やメソッド（操作）を持つことが出来る
- 「オブジェクト」＝クラスをインスタンス化し、データ（プロパティ）とそれに対する操作（メソッド）をまとめたもの。  
  <br>

`$item`は Author.php の book メソッドを利用できる  
そのため、リレーションの有無の確認や Book.php の getTitle メソッドを用いて本のタイトルを表示できるようになっている。  
<br>

## has Many 結合

１対多の関係のリレーション  
「has One」と同様、主テーブル側に用意する  
「has Many」は主テーブルから従テーブルのレコードを複数取得できる  
<br>

### Author モデル修正

Author.php の book メソッドの下部に books メソッドを追記

```
public function books(){
  return $this->hasMany('App\Models\Book');
}
```

<br>

### index.blade.php の修正

```
//略

@section('content')
<table>
  <tr>
    <th>Author</th>
    <th>Book</th>
  </tr>
  @foreach ($items as $item)
  <tr>
    <td>
      {{$item->getDetail()}}
    </td>
    <td>
      @if ($item->books != null)
      <table width="100%">
        @foreach ($item->books as $obj)
        <tr>
          <td>{{ $obj->getTitle() }}</td>
        </tr>
        @endforeach
      </table>
      @endif
    </td>
  </tr>
  @endforeach
</table>
@endsection
```

`$item->books`で１つ以上のリレーションを確認出来たら、１つずつ`$obj`として本のタイトルを表示するようにしている。  
<br>

## belongs To 結合

従テーブルから主テーブルを取り出すメソッド  
「has One」同様、１つのレコードが取り出せる  
<br>

## Book モデルの修正

Book モデルの Book クラス内に author メソッドを追記し、getTitle メソッドを修正。

```
//略

  public function getTitle(){
        return 'ID'.$this->id . ':' . $this->title . ' 著者:' . optional($this->author)->name;
    }
  public function author(){ //追記
        return $this->belongsTo('App\Models\Author');
    }
```

optional メソッドを使って author に紐づいていない book のデータも考慮している。  
<br>

## リレーションの有無

has メソッドと doesntHave メソッドでリレーションの有無を確認できる。  
<br>

リレーションの値を持つ  
`モデル::has(モデル内で定義したメソッド名)->get();`  
リレーションの値を持たない  
`モデル::doesntHave(モデル内で定義したメソッド名)->get();`  
<br>

has メソッドと doesntHave の動作確認  
AuthorController.php の relate アクションを修正

```
public function relate()
{
    $hasItems = Author::has('book')->get();
    $noItems = Author::doesntHave('book')->get();
    $param = ['hasItems' => $hasItems, 'noItems' => $noItems];
    return view('author.index',$param);
}
```

has メソッドが実行されると、Author モデル内の Book メソッドが呼び出され、Book メソッドが hasOne のリレーションを返す。  
返されたリレーションを元に Laravel 内部でテーブルが結合される。  
それを get()で取得し、`$hasItems`に格納する。  
<br>

画面で確認するために resources/views/author/index.blade.php を修正

```
@extends('layouts.default')
<style>
th {
  background-color: #289ADC;
  color: white;
  padding: 5px 40px;
}

tr:nth-child(odd) td {
  background-color: #FFFFFF;
}

td table {
  margin: 0 auto;
}

td {
  padding: 25px 40px;
  background-color: #EEEEEE;
  text-align: center;
}

td table tbody tr td {
  background-color: #EEEEEE !important;
}
</style>
@section('title', 'author.index.blade.php')

@section('content')
<table>
  <tr>
    <th>Author</th>
    <th>Book</th>
  </tr>
  @foreach ($hasItems as $item)
  <tr>
    <td>
      {{$item->getDetail()}}
    </td>
    <td>
      <table>
        @foreach ($item->books as $obj)
        <tr>
          <td>{{ $obj->getTitle() }}</td>
        </tr>
        @endforeach
      </table>
    </td>
  </tr>
  @endforeach
</table>
<table>
  <tr>
    <th>Author</th>
  </tr>
  @foreach ($noItems as $item)
  <tr>
    <td>{{ $item->getDetail() }}</td>
  </tr>
  @endforeach
</table>
@endsection
```

Book とリレーションを持つ Author が最初に表示され、その下にリレーションを持たない一覧が表示される。

<br><br>

# モデルのリレーションを深める

## N + 1 問題

データベースのアクセス回数によりアプリケーションの負荷が重くなってしまうこと。  
N 件のデータを持つテーブルと紐づいている別のテーブルで、以下の自体が発生する。

1. N 件のデータを持つテーブルを読みだす
2. 紐づいている別のテーブルから、[1]のテーブル各行に紐づくデータを１件ずつ読み出す際に計 N 回
   <br>

テーブルのデータを全て取得してから、関連づいているデータを取り出す処理をしている状態が「N + 1」状態。  
<br>

Laravel では with メソッドを使ってこの問題を解決できる  
`モデル::with(リレーション名)->get();`  
with メソッドはレコードの取得方法が通常と異なり、２回のデータベースへのアクセスで同じデータを取得することが出来る。  
<br>

BookController.php の index アクションに実装

```
public function index(Request $request){
-    $items = Book::all();
+    $items = Book::with('author')->get();
    return view('book.index', ['items'=>$items]);
}
```

<br>

- 前回までの実行結果

```
public function index(Request $request)
{
    $items = Book::all();
    foreach ($items as $item) {
        $item->author;
    }
    return view('book.index', ['items' => $items]);
}
```

この処理の時のクエリ

```
# 1回目
select * from `books`

# 2回目以降
select * from `authors` where `authors`.`id` = 1 limit 1
```

Book 全体を取得（１回目）後、Book １つ目に対する Author の処理（２回目）、Book ２つ目に対する Author の処理（３回目）...を Book の数だけ繰り返している。

全体取得(1) + N 件取得に対して N 回処理＝ N + 1 問題

<br>

with メソッドの時の実行結果

```
public function index(Request $request)
{
    $items = Book::with('authors')->get();
    return view('book.index', ['items' => $items]);
}
```

この処理の時のクエリ

```
# 1回目
select * from `books`
＃2回目
select * from `authors` where `authors`.`id` in (1, 3)
```

Book 全体を取得（１回目）後、紐づいている Author へのみ、同時処理。（２回目）  
with メソッドを使うと、テータベースへのアクセス２回で関連データを取得できる。  
<br>

## 多対多のリレーション

著者(Authors)、本(Books)の対して、本のレビューをする著者(reviews)がいて、  
reviews テーブルが author_id、book_id を持つとき、中間テーブルとして機能し、多対多のリレーション問題を解消できる。  
<br>
中間テーブルとなる reviews テーブルの定義
|カラム| 内容|
|-----|------|
|id| レコードに割り振られる ID|
|author_id| 関連する authors テーブルのレコード ID|
|book_id| 関連する book テーブルのレコード ID|
|review| レビュー内容|

上記のような中間テーブルのデータを経由して関連するテーブルのデータを取得する際は、`belongsToMany`メソッドを利用する。

例）Author.phpに以下のメソッドを定義
```
public function reviews()
{
    return $this->belongsToMany(Book::class);
}
```
`belongsToMany(取得したいクラス名::class)`  
reviewsメソッドを利用すると、Booksテーブルのデータとreviewsテーブルの外部キーとなるauthor_idとbook_idを取得できる。  
<br>
中間テーブルの固有データを利用するときはwithPivotメソッドを利用する  

例）reviewカラム（レビュー内容）のデータも取得したい時
```
public function reviews()
{
    return $this->belongsToMany(Review::class)->withPivot('review');
}
```