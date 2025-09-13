---
tags:
  - Laravel基礎
---

# 基本的なアプリケーション作成

データ一覧・追加・更新・削除などが出来るアプリケーションを作成
<br>

## ディレクトリ作成

```
advance-laravel
├── docker
│   ├── mysql
│   │   ├── data
│   │   └── my.cnf
│   ├── nginx
│   │   └── default.conf
│   └── php
│       ├── Dockerfile
│       └── php.ini
├── docker-compose.yml
└── src
```

<br>

## ディレクトリ作成後の環境構築

1. それぞれのコンテナの設定ファイルを記述する
2. docker-compose コマンドでコンテナを作成
3. Composer コマンドで Laravel のプロジェクトを作成する
4. 時間設定を編集
   <br>

※Laravel プロジェクト作成後、ウェルカームページで「Permission denied」エラーが発生した場合の対応。  
~/coachtech/laravel/advance-laravel 直下で sudo コマンド実行  
`$ sudo chmod -R 777 src/*`  
<br>

## データベースの確認

mySQL コンテナで以下コマンド実行  
`$ mysql -u laravel_user -p`  
MYSQL_PASSWORD で設定したパスワードでログイン出きれば成功  
<br>
show database コマンドでデータベース確認  
MYSQL_DATABASE で設定した laraval_db が作成されていれば成功  
<br>

## データベースの接続

データベースの存在が確認出来たら、laravel のプロジェクトとデータベースを接続。  
接続の際は、Laravel のプロジェクトの中の「.env」ファイルを編集  
<br>
.env

```
  DB_CONNECTION=mysql
- DB_HOST=127.0.0.1
+ DB_HOST=mysql
  DB_PORT=3306
- DB_DATABASE=laravel
- DB_USERNAME=root
- DB_PASSWORD=
+ DB_DATABASE=laravel_db
+ DB_USERNAME=laravel_user
+ DB_PASSWORD=laravel_pass
```

<br>
|設定箇所|	dockerとの対応|	解説|
|-------|----------------|----|
|DB_HOST|	サービス名 mysql|	データベースのホスト名を指定します。|
|DB_DATABASE	|MYSQL_DATABASE	|使用するデータベース名を指定します。|
|DB_USERNAME|	MYSQL_USER|	使用するユーザー名を指定します。|
|DB_PASSWORD	|MYSQL_PASSWORD	|使用するユーザのパスワードを指定します|
<br>

※書き込み権限エラーが出た場合  
原因：ウェルカムページ確認の際、エラー対応で sudo コマンドを実行しているため。

1. ファイルの所有者と権限を確認  
   advance-laravel で以下コマンド実行  
   `ls -l ~/coachtech/laravel/advance-laravel/src/.env`  
   出力例）`-rw-r--r-- 1 root root 1234 Jul 28 21:00 .env`  
   「root root」となっている場合、自分のユーザーが書き込み権限を持っていない。

2. （ディレクトリごと）所有者を自分のユーザーに変更  
   `sudo chown -R seki:seki /home/seki/coachtech/laravel/advance-laravel`

<br><br>

# データの作成：マイグレーション編

## Laravel におけるデータベース操作

Eloquent ORM を使用してサポートしている様々なデータベースとのやり取りを行う。  
<br>

Laravel がサポートしているデータベース

- MySQL
- PostgreSQL
- SQLite
- SQL Server
  <br>

## マイグレーションとは

MySQL を直接記述することなく、データベースのテーブルの管理が行える機能。  
テーブルの作成や削除、定義の変更などを行う機能がある。  
<br>
マイグレーションにより、チームメンバーにデータベースのテーブルを共有できる。  
テーブル設計を変えた後でも共有可能。  
<br>

## マイグレーションによるテーブル作成の流れ

1. マイグレーションファイルの作成
2. マイグレーションの実行
   <br>

## マイグレーションファイルの作成

PHP コンテナ内で下記コマンド実行により、ひな形を元に自動で生成尻ことが出来る。  
`php artisan make:migration create_[テーブル名]_table`  
今回は authors テーブル作成  
<br>

作成したマイグレーションファイルを確認  
src/database/migration ディレクトリに以下のファイルが用意されている

- xxXX_create_users_table.php（プロジェクト作成時）
- xxxx_create_password_resets_table.php（プロジェクト作成時）
- xxxx_create_failed_jobs_table.php（プロジェクト作成時）
- xxxx_create_authors_table.php（コマンドで新規作成）
  <br>

## マイグレーションファイルへの記述

xxxx_create_authors_table.php 内の up メソッドと down メソッドを利用する。

- 「up メソッド」＝新しいテーブル、カラムを追加するために使用。
- 「down メソッド」＝ロールバック（元に戻す作業）の内容を設定。
  <br>

## テーブル生成時の記述

authors テーブルの内容
|カラム| 説明| 制約等|
|------|-----|------|
|id| 主キー| 自動作成(AUTO_INCREMENT), 主キー制約(PRIMARY KEY)|
|name |名前 |文字列(100)|
|age| 年齢| 数値|
|nationality| 国籍| 文字列(100)|
|created_at |作成日時 |デフォルトで実行時刻が入る|
|updated_at| 更新日時| デフォルトで実行時刻が入る|
<br>

up メソッドを修正

```
public function up()
   {
       Schema::create('authors', function (Blueprint $table) {
           $table->id();
           $table->string('name', 100);
           $table->integer('age');
           $table->string('nationality', 100);
           $table->timestamp('created_at')->useCurrent()->nullable();
           $table->timestamp('updated_at')->useCurrent()->nullable();
       });
   }
```

<br>

### テーブル名の設定

`Schema::create( テーブル名 , function (Blueprint $table)`  
コマンドでファイルを作成した際、テーブル名が自動で入力されている。  
<br>

### カラムの設定

`$table->メソッド;`  
メソッドを使ってカラムを定義する

- id メソッド
  自動生成される主キーカラムの設定  
  `$table->id();`  
  MySQL の制約ではオートインクリメント（自動生成）とプライマリーキーが自動で設定される。  
  <br>

- string メソッド
  文字列型のカラムを作成  
  `$table->string( カラム名 , 文字列の長さ )`  
  null は許容されていない  
  <br>

- int メソッド
  数値型のカラムを作成  
  `$table->integer( カラム名 );`  
  null は許容されていない  
  <br>

- timestamp メソッド
  タイムスタンプカラムを作成  
  `$table->timestamp( カラム名 )`  
  useCurrent メソッドと nullable メソッドを併用することで、実行時刻が格納されるようにする。

```
$table->timestamp('created_at')->useCurrent()->nullable();
$table->timestamp('updated_at')->useCurrent()->nullable();
```

create_at と update_at はテーブルを作成する際、ほとんどの場合で活用するもの。  
マイグレーションを作成する際、テンプレートで入れるものと認識する。  
<br>

その他マイグレーションで利用できるメソッド  
https://readouble.com/laravel/8.x/ja/migrations.html#column-method-bigIncrements  
<br>

## マイグレーションの実行

ファイルが作成出来たらマイグレーションを実行  
PHP コンテナにて以下コマンド実行  
`php artisan migrate`  
<br>

## マイグレーションファイルの修正

マイグレーションファイルを修正した場合、再度マイグレートする必要がある。  
1 度マイグレートしたファイルは再度マイグレートが行われないようになっている。  
<br>

修正の流れ

1. 該当マイグレーションの内容を修正
2. php artisan migrate:rollback --step=1(直前の１つだけ戻す)
3. php artisan migrate（再実行）
   <br>

その他便利コマンド
| 目的 | コマンド例 |
| --------------------- | ----------------------------------------------------------------- |
| 直前の 1 つだけ取り消す | `php artisan migrate:rollback --step=1` |
| 特定のマイグレーションだけ再実行 | `php artisan migrate:refresh --path=/database/migrations/xxx.php` |
| 全部 1 回ずつ巻き戻す | `php artisan migrate:rollback`（複数回実行すれば順に戻る） |
| 最後の〇件だけ実行を取り消す | `php artisan migrate:rollback --step=3`（例：最後の 3 件） |
| データは保持したままテーブル再作成（慎重） | `php artisan migrate:reset`（一括 down）+ `php artisan migrate`（再 up） |
<br>

最終兵器  
`php artisan migrate:fresh`  
※上記コマンドを実行すると全テーブルの全データが消し飛ぶ。

<br><br>

# データ作成：シーディング編

## シーディングとは

Laravel にはシーダークラスというものがあり、データベースへテスト用の初期データを簡単に投入できる。（ダミーデータ）  
マイグレーションでデータベースを構築し、シーディングで初期データを投入するようにしておくと、いつでもデータベースの再構築と初期データの投入までを行える。  
他人への提供も可能。
<br>

## シーディングの流れ

1. シーダーファイルの作成
2. シーダーファイルの登録
3. シーディングの実行
   <br>

## シーダーファイルの作成

シードを作成するためのスクリプトファイルを生成  
PHP コンテナから以下のコマンド実行  
`php artisan make:seeder AuthorsTableSeeder`  
database/seeders ディレクトリにファイルが作成される。  
<br>

### シードを作成

run メソッドに authors テーブルのシードを作成する処理を用意

```
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
+ use Illuminate\Support\Facades\DB;

class AuthorsTableSeeder extends Seeder
{
  /**
  * Run the database seeds.
  *
  * @return void
  */
  public function run()
  {
+    $param = [
+      'name' => 'tony',
+      'age' => 35,
+      'nationality' => 'American'
+    ];
+    DB::table('authors')->insert($param);
+    $param = [
+      'name' => 'jack',
+      'age' => 20,
+      'nationality' => 'British'
+    ];
+    DB::table('authors')->insert($param);
+    $param = [
+      'name' => 'sara',
+      'age' => 45,
+      'nationality' => 'Egyptian'
+    ];
+    DB::table('authors')->insert($param);
+    $param = [
+      'name' => 'saly',
+      'age' => 31,
+      'nationality' => 'Chinese'
+    ];
+    DB::table('authors')->insert($param);
  }
}
```

`$param`に連想配列で格納されたデータをクエリビルダの insert メソッドでデータベースに挿入している。  
<br>

## シーダーファイルの登録

シーダーファイルを DatabaseSeeder.php に登録するためにスクリプトを修正  
<br>

DatabaseSeeder.php

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
+        $this->call(AuthorsTableSeeder::class);
    }
}
```

run メソッドにシーダーを実行する処理を記述  
call メソッドでシーダーを指定することで、そのシーダーファイルのシーディング処理が実行されるようになっている。  
`$this->call(シーダークラス::class);`  
<br>

## シーディングの実行

PHP コンテナ内でシーディング処理実行  
`php artisan db:seed`  
DatabaseSeeder.php の run アクションが実行され、そこから各シーダーファイルの run アクションも呼び出され実行される。  
authors テーブルにデータが挿入される  
phpMyAdmin を確認：http://localhost:8080/

<br><br>

# CRUD 処理

- C(Create)＝追加
- R(Read)＝取得
- U(Updata)=更新
- D(Delete)=削除
  <br>

SQL 対応文
|CRUD| SQL 文| 機能|
|----|-------|-----|
|Create| INSERT| データ追加|
|Read |SELECT |データ取得|
|Update| UPDATE| データ更新|
|Delete |DELETE |データ削除|
<br>

## データベースのデータを扱うためには

DB クラス、クエリビルダ、Eloquent を用いる。

- DB クラス、クエリビルダ ＝ SQL に近い文法
- Eloquent ＝ PHP に近い文法
  <br>

## Eloquent

- 「Eloquent(エロクアント)」＝ Laravel にデフォルトで搭載されている ORM
- 「ORM」＝ PHP のような記述でより直感的にデータベースを操作する方法
  <br>
  １つのテーブルに対し、１つの Eloquent モデルを紐づける。  
  Eloquent モデルに対応したテーブルに対し、データの追加・取得・変更・削除を行うことができる。  
  Eloquent の使用にはモデルの作成が必要になる。  
  <br>

## Eloquent の使用

データベースとのやり取りを行うのはコントローラであるため、コントローラ内で Eloquent を使用する。

1. テーブルを作成する
2. テーブルに紐づくモデルを作成する
3. コントローラで使用するモデルファイルを読み込む
   <br>

### モデルとは

「MVC」の M にあたる部分。データの処理や制御を行う。  
Eloquent ORM（DB のデータを操作する機能）とビジネスロジックを持ったクラスのこと  
<br>

## モデルの作成

PHP コンテナ内で下記コマンド実行  
`php artisan make:model Author`  
app/models ディレクトリに Author.php ファイルが作成される  
<br>
これで Authors テーブルに Author モデルが紐づいている  
※テーブル名は複数形、モデル名は単数形という決まりがある。  
<br>

## コントローラにモデルファイルを読み込む

データベースとのやり取りを行うコントローラファイルを作成する  
`php artisan make:controller AuthorController`  
<br>

作成したコントローラにモデルファイルを読み込む  
AuthorController.php

```

<?php

namespace App\Http\Controllers;
+ use App\Models\Author;
use Illuminate\Http\Request;

class AuthorController extends Controller
{
    //
}
```

Author モデルを読み込めたため、Eloquent を使用できるようになった。

<br><br>

# CRUD 処理：データの取得

## レイアウトの利用

データの表示に使用するレイアウトの見た目を整える  
「resources>views」ディレクトリに layouts というディレクトリを作成し、その中に default.blade.php ファイルを作成。  
<br>

default.blade.php ファイルを修正

```
 <!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>@yield('title')</title>
  <style>
    body {
      font-size:16px;
      margin: 5px;
    }
    h1 {
      font-size:60px;
      color:white;
      text-shadow:1px 0 5px #289ADC;
      letter-spacing:-4px;
      margin-left: 10px
    }
    .content {
      margin:10px;
    }
  </style>
</head>
<body>
  <h1>@yield('title')</h1>
  <div class="content">
    @yield('content')
  </div>
</body>
</html>
```

<br>

## データを取得

データを取得するためにコントローラの記述が必要  
AuthorController.php に index アクションを用意

```
<?php

namespace App\Http\Controllers;

// Eloquentを使用できるようにAuthorモデルを読み込む
use App\Models\Author;
use Illuminate\Http\Request;

class AuthorController extends Controller
{
    public function index()
    {
        $authors = Author::all();
        return view('index', ['authors' => $authors]);
    }
}
```

Author クラスの all メソッドを利用し、authors テーブルから全件取得を行っている。  
<br>

Eloquent を利用して取得したデータは foreach 文などで値を加工することが出来る。  
Eloquent などで取得したデータの型は、Laravel のオブジェクトのコレクションという型になる。  
また、下記の記述でデータ全件が格納された authors をパラメータとして渡し、index.blade.php を呼び出している。  
`return view('index', ['authors' => $authors]);`  
<br>

## データ一覧の表示

取得したデータを表示するためのページを作成  
resources/views 内に index.blade.php を作成

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
@section('title', 'index.blade.php')

@section('content')
<table>
  <tr>
    <th>id</th>
    <th>name</th>
    <th>age</th>
    <th>nationality</th>
  </tr>
  @foreach ($authors as $author)
  <tr>
    <td>{{$author->id}}</td>
    <td>{{$author->name}}</td>
    <td>{{$author->age}}</td>
    <td>{{$author->nationality}}</td>
  </tr>
  @endforeach
</table>
@endsection
```

AuthorController から渡されたパラメータを使用している。  
<br>

## ルーティングを設定

/に get でアクセスしたときに AuthorController の index アクションが呼び出されるよう、ルーティングを設定。  
<br>

web.php

```
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\AuthorController;

- Route::get('/', function () {
-     return view('welcome');
- });

+ Route::get('/', [AuthorController::class, 'index']);
```

http://localhost/ にアクセスして表示確認  
<br>

## シングルアロー演算子とダブルアロー演算子

| アロー演算子         | Header Two | 使用用途                                 |
| -------------------- | ---------- | ---------------------------------------- |
| シングルアロー演算子 | ->         | 配列へのアクセス（配列から値を取り出す） |
| ダブルアロー演算子   | =>         | 配列に値を代入するとき                   |

<br>
シングルアロー演算子はキーの値を取り出すときに利用  
ダブルアロー演算子はキーに対して値を代入するときに利用  
<br>

## まとめ

- Eloquent の all メソッドを使用してデータを全件取得できる
- 取得したデータはコレクションとして格納され、複数のレコードが複数の配列となって格納される。
- テンプレート内では foreach 文を利用して１つずつレコードを取り出して表示する

<br><br>

# CRUD 処理：データの追加

追加機能の作成の流れ

1. データ追加用ページの作成

- データ追加用ページのテンプレート作成
- アクションの作成

2. データ追加処理

- モデルの修正
- アクションの作成
  _ 追加するためのデータの取得
  _ データベースに追加
  <br>

## データ追加用ページの作成

add.blade.php（データ追加用ページ）を作成  
追加用ページにはレコードの内容を記述フォームが必要  
また、データベースに登録するために追加可能な値をフォームで送信する必要がある。  
<br>

Authors テーブルの構造を確認
|カラム名| 型|
|-------|----|
|id | id |
|name| string|
|age| intager|
|nationality| string|
|created_at| timestamp|
|updated_at| timestamp|

上記のうち、id,created_at,updated_at カラムは MySQL によって自動的にデータが挿入されるようになっている。  
そのため、name,age,nationality の値を入力するフォームを作成。  
<br>

resources/views 内に add.blade.php ファイルを作成  
下記を記述

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
  button {
    padding: 10px 20px;
    background-color: #289ADC;
    border: none;
    color: white;
  }
</style>
@section('title', 'add.blade.php')

@section('content')
<form action="" method="post">
  <table>
  @csrf
    <tr>
      <th>name</th>
      <td><input type="text" name="name"></td>
    </tr>
    <tr>
      <th>age</th>
      <td><input type="text" name="age"></td>
    </tr>
    <tr>
      <th>nationality</th>
      <td><input type="text" name="nationality"></td>
    </tr>
    <tr>
      <th></th>
      <td><button>送信</button></td>
    </tr>
  </table>
</form>
@endsection
```

`@extends('layouts.default')`によって、先ほど作成した default.blade.php をレイアウトとして読み込んでいる。  
form タグの action 属性はルーティングを設定していないため、一旦空にしておく。  
フォームを利用する際は form タグの開始タグ直後に必ず@csrf をつける  
<br>
※Laravel における CSRF 攻撃対策とトークンの仕組み  
「CSRF(クロスサイトリクエストフォージェリ)」＝認証済みユーザーに代わって不正なコマンドを実行する、悪意のある攻撃の一種。

「CSRF トークン」＝アクティブなユーザーに対して Laravel が自動的に生成。認証済みユーザーが実際にアプリケーションへリクエストを行っているユーザーであるかを判定する。  
Laravel では@csrf がないとリクエストを受け取れないように設定されている。  
<br>

### アクションを作成

データ追加用ページをブラウザに表示するためのアクションを作成  
<br>

AuthorController.php

```

// データ追加用ページの表示
public function add(){
       return view('add');
}
```

add アクションでデータ追加用ページを呼び出す処理を記述  
<br>

### ルーティングを設定

/add にアクセスしたときに AuthorController の add アクションが呼び出されるよう、ルーティングを設定。

web.php

```

<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\AuthorController;

Route::get('/add', [AuthorController::class, 'add']);
```

<br>
http://localhost/add にアクセスし、追加用ページが表示されるか確認。  
<br>

## データの追加機能を作成

### モデルの設定

Laravel ではカラムに対して書き換え可・不可の設定を行わないとデータの登録が出来ず、エラーが発生する。  
そのため、モデルファイルで書き換えの設定を行う。  
|設定| プロパティ|
|----|-----------|
|書き換え可能| $fillable|
|書き換え不可| $guarded|
<br>

Http/Models/Author.php を修正

```
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Author extends Model
{
  use HasFactory;

  protected $fillable = ['name', 'age', 'nationality'];
}
```

<br>

### リクエストボディからデータを取得

データ追加用ページのフォームに入力した値を取得する

AuthorController.php を修正

```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\Author;

class AuthorController extends Controller
{
    public function add(){
        return view('add');
    }

    public function create(Request $request){
        $form = $request->all();
        return redirect('/');
    }

    public function index()
    {
        $authors = Author::all();
        return view('index', ['authors' => $authors]);
    }
}
```

フォームから送信された値はリクエストボディに含まれる。  
form タグ内の input タグの name 属性が key、input タグに入力された値が value となり、連想配列として送信される。  
create アクションの引数に`Request $request`を指定することでリクエストボディを受け取れる。  
`$request->all`でリクエストの全ての情報を取得  
<br>
※「リクエストボディ」＝ HTTP リクエストを構成する要素の１つ  
POST メソッドの場合に送信する form タグ内に入力した値が格納される。  
<br>

## Eloquent を使用してデータを保存する

データを取得出来たため、データベースに追加する。  
追加機能では Eloquent の Create メソッドを使用  
<br>

AuthorController.php を修正

```
<?php

namespace App\Http\Controllers;

use App\Models\Author;
use Illuminate\Http\Request;

class AuthorController extends Controller
{

  public function add()
  {
    return view('add');
  }

  public function create(Request $request)
  {
    $form = $request->all();
    + Author::create($form);
    return redirect('/');
  }

  public function index()
    {
        $authors = Author::all();
        return view('index', ['authors' => $authors]);
    }
}
```

create メソッドでテーブルにデータを挿入している  
データ追加用ページの input タグの name 属性がテーブルのカラム名と一致しているため、create メソッドの引数に`request->all()`の値を代入することで、そのままテーブルに保存することが出来る。  
データ挿入後は一覧画面に遷移するようリダイレクトしている。  
<br>

※name 属性がテーブルのカラム名と一致してない場合は連想配列で渡す

```
$form = [
    'name' => $request->name,
    'age' => $request->age,
    'nationality' => $request->country,
];
Author::create($form);
```

<br>

## ルーティングの設定

/add に post メソッドでアクセスされた際、AuthorController の create アクションが呼び出されるように設定。  
<br>

web.php

```

<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\AuthorController;

Route::get('/add', [AuthorController::class, 'add']);
Route::post('/add', [AuthorController::class, 'create']);
Route::get('/', [AuthorController::class, 'index']);
```

同じ URL でも HTTP メソッドが違えば、呼び出すアクションを変えることが出来る。  
<br>

ルーティングの設定後、データ追加用ページの form タグ内の action 属性を指定。

```

<form action="/add" method="post">
  <table>
  @csrf
    <tr>
      <th>name</th>
      <td><input type="text" name="name"></td>
    </tr>
    <tr>
      <th>age</th>
      <td><input type="text" name="age"></td>
    </tr>
    <tr>
      <th>nationality</th>
      <td><input type="text" name="nationality"></td>
    </tr>
    <tr>
      <th></th>
      <td><button>送信</button></td>
    </tr>
  </table>
</form>
```

これにより、フォームを入力し送信すると、AuthorController の create アクションが呼び出されるようになる。  
<br>

http://localhost/add にアクセスし、入力・送信して確認。  
※入力欄を埋めず、空で送信するとエラーが発生する。

<br><br>

# CRUD 処理：データの更新

更新機能の作成の流れ

1. データ編集用画面の作成

- データ編集用ページのテンプレート作成
- アクションの作成

2. データ更新処理

- 更新対象のレコードを取得
- 更新後の値の取得
- データベースに更新
  <br>

## データ編集画面の作成

### データ編集用ページのテンプレート作成

resources/views ディレクトリ内に edit.blade.php 作成・記述

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
    button {
      padding: 10px 20px;
      background-color: #289ADC;
      border: none;
      color: white;
    }
</style>
@section('title', 'edit.blade.php')

@section('content')
<form action="/edit" method="POST">
  <table>
    @csrf
    <tr>
      <th>
        id
      </th>
      <td>
        <input type="text" name="id" value="{{$form->id}}">
      </td>
    </tr>
    <tr>
      <th>
        name
      </th>
      <td>
        <input type="text" name="name" value="{{$form->name}}">
      </td>
    </tr>
    <tr>
      <th>
        age
      </th>
      <td>
        <input type="text" name="age" value="{{$form->age}}">
      </td>
    </tr>
    <tr>
      <th>
        nationality
      </th>
      <td>
        <input type="text" name="nationality" value="{{$form->nationality}}">
      </td>
    </tr>
    <tr>
      <th></th>
      <td>
        <button>送信</button>
      </td>
    </tr>
  </table>
</form>
@endsection
```

編集画面には更新対象者の現在のデータが表示されている  
input の value 属性に値を指定することで、表示している。  
<br>

### アクションの作成

データ編集ページを呼び出すアクションを作成  
AuthorController.php にアクションを追加

```
<?php

namespace App\Http\Controllers;

use App\Models\Author;
use Illuminate\Http\Request;

class AuthorController extends Controller
{
　　　　　　　　// データ一覧ページの表示
    public function index(){
        $authors = Author::all();
        return view('index', ['authors' => $authors]);
   }

   // データ追加用ページの表示
    public function add(){
        return view('add');
    }

    // データ追加機能
    public function create(Request $request){
        $form = $request->all();
        Author::create($form);
        return redirect('/');
    }

	// 追記：ここから
    // データ編集ページの表示
    public function edit(Request $request){
        $author = Author::find($request->id);
        return view('edit', ['form' => $author]);
    }
  // 追記：ここまで
}
```

どのデータを更新するかは基本的に id で指定する。  
`$author = Author::find($request->id);`  
`$request->id`でクエリパラメータから id を取得できる。  
ID によるレコード検索は find メソッドを利用する。  
※プライマリキーのフィールド名が id でないとうまく動作しない。  
<br>
データ編集用ページでは新対象の現在地が表示されている  
取得した更新対象のデータを edit.blade.php にパラメータとして渡している  
<br>

### ルーティングの設定

/edit に get メソッドでアクセスした際、AuthorController.php の edit アクションが呼び出されるよう記述。  
<br>

web.php

```
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\AuthorController;

Route::get('/', [AuthorController::class, 'index']);
Route::get('/add', [AuthorController::class, 'add']);
Route::post('/add', [AuthorController::class, 'create']);
Route::get('/edit', [AuthorController::class, 'edit']);
```

<br>
http://localhost/edit?id=1 にアクセスし、id=1のデータが表示されたら成功。  
<br>

## データ更新処理

データ編集用ページから送信されたフォームの値を取得し、データベースにデータの更新を保存する。  
AuthorController.php に update アクションの追加を記述

```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\Author;

class AuthorController extends Controller
{
    // データ一覧ページの表示
    public function index()
    {
        $authors = Author::all();
        return view('index', ['authors' => $authors]);
   }

   // データ追加用ページの表示
    public function add()
    {
        return view('add');
    }

    // 追加機能
    public function create(Request $request){
        $form = $request->all();
        Author::create($form);
        return redirect('/');
    }
    // データ編集ページの表示
    public function edit(Request $request){
        $author = Author::find($request->id);
        return view('edit', ['form' => $author]);
    }

    // 更新機能
    public function update(Request $request)
    {
        $form = $request->all();
        unset($form['_token']);
        Author::find($request->id)->update($form);
        return redirect('/');
    }
}
```

find メソッドの引数にリクエストで取得した id を指定  
これにより更新対象のレコードを取得し、そのレコードを update メソッドで`$form`の内容を元にして更新している。  
<br>

blade ファイルの@csrf により、csrf 対策用のトークンが生成される  
データベースへ保存するときは必要ないため、`unset($form['_token'])`で削除している。  
<br>

### ルーティングの設定

web.php

```
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\AuthorController;

Route::get('/', [AuthorController::class, 'index']);
Route::get('/add', [AuthorController::class, 'add']);
Route::post('/add', [AuthorController::class, 'create']);
Route::get('/edit', [AuthorController::class, 'edit']);
Route::post('/edit', [AuthorController::class, 'update']);
```

http://localhost/edit?id=1 にアクセスし、フォームの値を変更・送信。  
データが更新されていたら成功

<br><br>

# CRUD 処理：データの削除

削除機能の作成の流れ

1. データ削除用画面の作成

- データ削除用ページのテンプレート作成
- アクションの作成

2. データ削除処理

- 削除対象のレコードを取得
- データベースからレコードを削除
  <br>

## データ削除用ページの作成

### データ削除用ページのテンプレート作成

resources/views ディレクトリ内に delete.blade.php を作成・記述

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
  button {
    padding: 10px 20px;
    background-color: #289ADC;
    border: none;
    color: white;
  }
</style>
@section('title', 'delete.blade.php')

@section('content')
<table>
    <tr>
      <th>id</th>
      <td>{{$author->id}}</td>
    </tr>
    <tr>
      <th>name</th>
      <td>{{$author->name}}</td>
    </tr>
    <tr>
      <th>age</th>
      <td>{{$author->age}}</td>
    </tr>
    <tr>
      <th>nationality</th>
      <td>{{$author->nationality}}</td>
    </tr>
    <tr>
      <th></th>
      <td>
        <form action="/delete" method="POST">
            @csrf
            <button>送信</button>
        </form>
    </td>
    </tr>
</table>

@endsection
```

<br>

### アクションの作成

AuthorController.php に delete アクションを作成

```
// データ削除用ページの表示
    public function delete(Request $request)
    {
        $author = Author::find($request->id);
        return view('delete', ['author' => $author]);
    }
```

<br>

### ルーティングの設定

/delete にアクセスしたときに AuthorController.php の delete アクションが呼び出されるように、web.php に記述。

```

<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\AuthorController;

Route::get('/', [AuthorController::class, 'index']);
Route::get('/add', [AuthorController::class, 'add']);
Route::post('/add', [AuthorController::class, 'create']);
Route::get('/edit', [AuthorController::class, 'edit']);
Route::post('/edit', [AuthorController::class, 'update']);
Route::get('/delete', [AuthorController::class, 'delete']);
```

<br>
http://localhost/delete?id=4 にアクセスし、削除用ページが表示されるか確認。  
<br>

## データ削除処理

削除対象の id を取得し、その id に対応するレコードをデータベースから削除する。  
AuthorController.php に remove アクションを追加

```
 // 削除機能
    public function remove(Request $request)
    {
        Author::find($request->id)->delete();
        return redirect('/');
    }
```

find メソッドの引数に id を指定し、削除対象のレコードを取得。  
レコードを delete メソッドで削除  
<br>

### ルーティングの設定

/delete に post メソッドでアクセスしたときに、AuthorController.php の remove アクションが呼び出されるように web.php に記述。

```
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\AuthorController;

Route::get('/', [AuthorController::class, 'index']);
Route::get('/add', [AuthorController::class, 'add']);
Route::post('/add', [AuthorController::class, 'create']);
Route::get('/edit', [AuthorController::class, 'edit']);
Route::post('/edit', [AuthorController::class, 'update']);
Route::get('/delete', [AuthorController::class, 'delete']);
Route::post('/delete', [AuthorController::class, 'remove']);
```

<br>
現時点では削除用ページの送信を押してもエラー表示になる  
```
エラー
null でのメンバ関数 delete() の呼び出し
```
delete.blade.phpはフォーム内に送信するデータがないため、削除対象のidをコントローラに渡せていない（nullの原因）。  
削除対象のidをクエリパラメータに指定し、リクエストに含めるようにdelete.blade.phpを修正。  
```
      <td>
        <form action="/delete?id={{$author->id}}" method="POST">
            @csrf
            <button>送信</button>
        </form>
    </td>
```
action属性にクエリパラメータを指定し、削除対象のidが代入されるよう記述。  
<br>

http://localhost/delete?id=4 にアクセス・送信し、id=4 のデータが削除できていれば成功。
