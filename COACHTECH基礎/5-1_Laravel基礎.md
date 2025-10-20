---
tags:
  - Laravel基礎
---

# フレームワーク

アプリケーションを作成する上での土台になるソフトウェア  
<br>

## Laravel

PHP を用いて開発する上でのフレームワーク  
「MVC モデル」採用  
<br>

## MVC モデル

「MVC モデル」＝アプリケーションの構成  
<br>
主要なパーツ
|構成するパーツ| 役割|
|-------------|-------|
|Routes| URL の情報を受け取る役割|
|Controller |URL から受け取った情報をもとにアプリケーションの操作を指示をする役割|
|Model| Controller からの命令をもとにデータの操作をする役割|
|View| Controller からの命令や Model から抽出されたをデータを画面に表示する役割|
<br>

## Laravel 開発環境

| イメージ名 | 説明                         | 役割                                           |
| ---------- | ---------------------------- | ---------------------------------------------- |
| nginx      | ローカルサーバのコンテナ     | 記述したプログラムを表示できるようにする       |
| php        | php のコンテナ               | PHP のプログラムを実行できるようにする         |
| mysql      | データベースのコンテナ       | データベースを管理できるようにする             |
| phpmyadmin | mysql の管理ツールのコンテナ | ブラウザからデータベースを確認できるようにする |

<br><br>

# Laravel インストール

## Composer

ライブラリなどのパッケージを管理するツール  
<br>

## Laravel のプロジェクトの作成

composer コマンドを使って Laravel のプロジェクトを作成する  
「プロジェクト」＝アプリケーションのベースとなるファイル  
<br>

composer はインストールされたコンテナから操作するため、  
docker-compose コマンドでログイン。  
`$ docker-compose exec php bash`  
<br>

composer コマンドで Laravel プロジェクト作成

```
$ composer create-project "laravel/laravel=8.*" . --prefer-dist
$ ls
```

<br>

出力結果

```
README.md  app  artisan  bootstrap  composer.json  composer.lock  config  database
package.json  phpunit.xml  public  resources  routes  server.php  storage  tests
vendor  webpack.mix.js`
```

<br>

プロジェクト作成時の composer コマンドの内容
|項目| 説明|
|---|------|
|composer| composer を実行するためのコマンド|
|create-project |プロジェクトを作成する時に使用するサブコマンド|
|"laravel/laravel=8.\*"| laravel のバージョン８を指定|
|. |ディレクトリ名の指定。今回はカレントディレクトリを指定|
|--prefer-dist| ダウンロード方法の指定。zip ファイルでダウンロードを指定|
<br>

Laravel のウェルカムページにアクセスできるようになる  
http://localhost/

<br>

## 時間設定の編集

VSCode で Laravel のプロジェクトを開き、config フォルダの app.php の 70 行目を確認  
`'timezone' => 'UTC',`

世界標準時から日本時間に修正  
`'timezone' => 'Asia/Tokyo',`  
<br>

PHP コンテナ上で時間確認  
`$ php artisan tinker`  
↓  
`>>> echo Carbon\Carbon::now();`  
<br>

出力結果  
`2022-04-05 17:26:36⏎`  
日本の現在時間が表示されば設定完了

<br><br>

# Laravel のコーディング規約

「コーディング規約」＝ファイルや変数などの命名規則やコーディングする際のルール  
開発組織よって変わる可能性があるため、開発組織に入った時に確認必須。  
<br>

## 命名規則

| 記法             | 使用する場所                           |
| ---------------- | -------------------------------------- |
| アッパーキャメル | コントローラ名、シーダファイル名       |
| ローワーキャメル | メソッド名                             |
| スネーク記法     | テーブル名、マイグレーションファイル名 |

<br>

### アッパーキャメル記法

先頭を大文字で書き始め、複合語はスペースをなくし、要素の先頭は大文字にする。  
モデル名、シーダーファイル名やコントローラ名に用いる。  
例）

- User.php
- BorderController.php

<br>

### ローワーキャメル記法

先頭を小文字で書き始め、複合語のスペースをなくし、要素の先頭は大文字にする。  
メソッド名に用いる。  
例）

- delete()
- getAuth()

<br>

### スネーク記法

単語と単語の区切りにアンダースコア(\_)を使う。  
テーブル名、マイグレーションファイル名に用いる。  
例）

- failed_jobs
- xxxx_create_samples_table

<br>

## スタイルガイド

Laravel には PSR-12 という推奨されているコーディング規約がある。  
PSR-12 公式ページ  
https://www.php-fig.org/psr/psr-12/

<br><br>

# ディレクトリとファイルの構成

## ディレクトリ構成

| ディレクトリ名 | 説明                                                                                                              |
| -------------- | ----------------------------------------------------------------------------------------------------------------- |
| app            | アプリケーションのコアとなるプログラムを保管するディレクトリです。                                                |
| Http           | コントローラーなどのアプリケーションの動作の起点となるプログラムを設置するディレクトリです。                      |
| Models         | モデルを扱うことができるディレクトリです。                                                                        |
| bootstrap      | アプリケーション実行時に最初に行われる処理がまとめられているディレクトリです。                                    |
| config         | アプリケーションの全設定ファイルがまとめられているディレクトリです。                                              |
| database       | データベースを作成するためのプログラムを保管するディレクトリです。                                                |
| public         | 全てのリクエストのエントリーポイント（Laravel の各処理の入り口）となる index.php が置かれているディレクトリです。 |
| resources      | コンパイルを必要とするプログラムファイル（ブレードなど）を保存するディレクトリです。                              |
| routes         | アプリケーションのルーティングを定義するディレクトリです。                                                        |
| vendor         | Composer でインストールされるパッケージなどを設置しているディレクトリです。                                       |
| tests          | アプリケーションの動作を確認するためのテストコードを置くためのディレクトリです。                                  |

<br>

## ファイル構成

| ファイル名        | 説明                                                                                           |
| ----------------- | ---------------------------------------------------------------------------------------------- |
| .env.example      | ローカル開発環境でデータベースを接続する時などに使われるファイルのテンプレートです。           |
| .env              | .env.example をコピーして、自分専用の設定に編集するためのファイルです。                        |
| .gitattributes    | 特定ファイルやディレクトリ毎に、Git 操作をカスタマイズするためのファイルです。                 |
| .gitignore        | GitHub でコードを共有する時に、GitHub のアップロードしたくないファイルを設定します。           |
| artisan           | artisan コマンドに関するファイルです。artisan コマンドはコントローラを作成する時等に使います。 |
| composer.json     | composer でインストールされたパッケージを記述しておくためのファイルです。                      |
| composer.lock     | composer でインストールされたパッケージの詳細情報を記録しているファイルです。                  |
| package.json      | JavaScript で使用されるパッケージを記述するためのファイルです。                                |
| package-lock.json | JavaScript で使用されるパッケージの詳細情報を記録しておくためのファイルです。                  |
| phpunit.xml       | プログラムの動作確認をする時に使われるファイルです。                                           |
| server.php        | Laravel にデフォルトで設定されているサーバーの起動に関するファイルです。                       |

<br>
.envファイルは個人個人のPC設定を施すためのファイルであり、  
他人に知られてはいけない設定値（パスワード等）が保存されている。  
GitHubにアップロードする際は、.gitignoreで設定する。

<br><br>

# ブラウザに画面を表示する

非エンジニアのユーザーにとって、ブラウザの見た目が変わらなければ  
何が起こっているか分からないため、アプリケーションが機能するためには、  
画面に何かを表示する必要がある。  
<br>
よって、画面を表示することが開発をする上での最初のステップとなる。  
<br>

## ブラウザに表示するまでの流れ

1. ユーザーが URL にログインする
2. ルーティングに従ってコントローラのアクションを呼び出す
3. コントローラで表示したいビューを指定する
4. 指定されたビューをブラウザに表示する

<br>

## ルーティングの設定

「ルーティング」＝ Web アプリケーションでの URL の扱い方を定義するもの。  
ルーティングを設定する前にコントローラが必要になる。
<br>
PHP コンテナの中で以下のコマンドを実行

```
$ docker-compose exec php bash
$ php artisan make:controller TestController
```

<br>
http://localhost にアクセスしたら、  
TestControllerのindexアクションを呼び出すルーティングを設定する。  
<br>

web.php に追記

```
    <?php

    use Illuminate\Support\Facades\Route;
+   use App\Http\Controllers\TestController;

-   Route::get('/', function () {
-        return view('welcome');
-   });
+   Route::get('/', [TestController::class, 'index']);
```

<br>

ルーティングの設定は一般的に下記のように記述する

```
use App\Http\Controllers\コントローラファイル名;

Route::HTTPメソッド('URLのルートパス', [コントローラ名, 'アクション名'])
```

「HTTP メソッド」には、HTTP プロトコルのリクエストメソッドである  
get,post,put,patch,delete などを使用することが出来る。

<br>

## コントローラの作成

「コントローラ」＝ルーティングで指定された URL のアクセスに対して、  
処理を行うためのクラス。  
コントローラでビューを指定することで、ブラウザに表示される内容を設定することが出来る。  
<br>

resources/views ディレクトリの中に index.php を作成  
`$ touch src/resources/views/index.php`  
<br>

app/Http/Controllers ディレクトリ内の TestController に  
コントローラを作成して、ビューを指定するプログラムを記述。

```
    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;

    class TestController extends Controller
    {
-       //
+       public function index()
+       {
+          return view('index');
+       }
    }
```

TestController コントローラに index アクションメソッドを定義している。  
アクションメソッドで関数を呼び出してビューを指定している。

<br>

### ビューファイルを作成する

前述で作成した index.php ファイルの中身を記述。

```
<!DOCTYPE html>
<html lang="ja">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>

<body>
  <h1>ブラウザに画面を表示できた！</h1>
</body>

</html>
```

<br>
Dockerコンテナを起動したうえでhttp://localhost/ にアクセスすると、  
index.phpの内容が表示される。  
<br>

## まとめ

ブラウザに画面を表示する実行順  
「ルーティング」⇒「コントローラ」⇒「ビュー」

<br><br>

# ブラウザに画面を表示する：controller 編

## コントローラとは

リクエストに応じて実行したい処理を記述したプログラム  
ルーティングに応じて、アプリケーション内部の司令塔の役割を果たす。  
<br>

## Controller を用いてビューを表示する

1. ビューファイルを作成（hello.php）
2. コントローラでビューを呼び出す
3. ルーティングを設定する

<br>

### コントローラでビューを呼び出す

コントローラを利用するにはファイルを作成する必要がある  
<br>

コントローラ命名規則

- アッパーキャメル記法で命名する（先頭が大文字）
- 最後に Controller を付け加える

```
$ docker-compose exec php bash
$ php artisan make:controller HelloController
```

<br>

コントローラにアクションを追加する  
「アクション」＝コントローラで行われる処理（関数）の単位  
<br>

app/Http/Controllers 内の HelloController に index アクションを作成

```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class HelloController extends Controller
{
+    public function index(){
+        return view('hello');
+    }
}
```

<br>

### ルーティングを設定

コントローラーで処理を割り当てるためにルーティングで指定

```
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\TestController;
+ use App\Http\Controllers\HelloController;

- Route::get('/', [TestController::class, 'index']);
+ Route::get('/test', [TestController::class, 'index']);
+ Route::get('/hello', [HelloController::class, 'index']);
```

<br>
useでコントローラをインポート  
web.phpの第２引数に[コントローラファイル名::class, 'アクション名']と定義  
<br>

アクセスして表示確認  
http:/localhost/hello

<br>

## コントローラからビューに値（パラメータ）を渡す

Web アプリケーションはデータベースから値を取得して、  
その値（パラメータ）をテンプレートに渡すことが多い。  
<br>

コントローラからビューにパラメータを渡すときの流れ

1. コントローラで view メソッドを使用して、ビューにパラメータを渡す
2. 渡されたパラメータをビューで表示する
   <br>

### コントローラでの処理

ビューにパラメータを渡すために、TestController.php を修正

```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class TestController extends Controller
{
    public function index(){
          $item = [
             'content' => 'パラメータを渡す',
         ];
         return view('index', $item);
    }
}
```

view メソッドの引数に以下を設定

- 第１引数：表示するテンプレートファイル名
- 第２引数：ビューに渡すパラメータ（連想配列）

<br>

### ビューでの処理

コントローラから渡されたパラメータをビューで表示するために、index.php を修正。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <h1>ブラウザに画面を表示できた！</h1>
+    <p><?php echo $content ?></p>
</body>
</html>
```

<br>
TestControllerから$itemという連想配列がビューに渡されている。  
連想配列$itemのキーである"content"をビューでは変数$contentとして直接使用できる。

### まとめ：パラメータの利用

1. コントローラ

- 連想配列に渡したい値を設定する
- view メソッドの第２引数にその連想配列を指定する

2. ビュー

- コントローラで指定された連想配列のキーを変数として使用する

<br>

## リクエストとレスポンス

「リクエスト」＝クライアントがサーバへアクセスする際、クライアントから送られる情報  
「レスポンス」＝サーバからクライアントへ返送する際の情報
<br>

controller.php  
`use Illuminate\Http\Request;`  
`use Illuminate\Http\Response;`

use でリクエストとレスポンスをインポートすることにより、  
リクエストやレスポンスが含むプロパティやメソッドが利用できるようになる。

<br><br>

# ブラウザに画面を表示する：route 編

## ルーティングとは

クライアント側からのリクエストに対して、サーバの処理うぃ紐づける作業。  
例）https://coachtech.com/ にアクセスしたらトップページを表示するという関連付け
<br>

## URL の読み方

`<https://example.com/text-book/welcome&test>`  
<br>
|構成要素| 該当箇所|
|-------|---------|
|プロトコル| http|
|ドメイン| http://example.com/|
|ルートパス |/text-book/welcome|
|クエリ文字列| &test|
<br>

## routes ディレクトリ

ルーティングに関する情報がまとめられている。  
<br>
|ファイル名| 説明|
|---------|--------|
|web.php| 一般的な Web ページとしてアクセスするためのルーティングファイルです。|
|api.php| API 用のルーティングファイルです。|
<br>
「API」＝ソフトウェアやアプリケーションの一部を外部に向けて公開することで、  
第三者が開発したソフトウェアと機能を共有できるようにするもの。  
<br>

## ルーティングの処理

### クライアントのリクエスト内容

1. HTTP メソッド：ユーザーからサーバに対する指示  
   GET（取得）、POST（送信）、PUT（更新）、DELETE（削除）など
2. リクエスト URL：ユーザーがアクセスする URL
3. クライアントがサーバに送信するデータ
   <br>

### サーバの処理

1. CRUD の処理  
   Create（登録）、Read（参照）、Update（更新）、Delete（削除）の４つ
2. レスポンス処理：サーバで実行された処理結果をクライアントに返す処理  
   <br>

web.php

```
<?php

use Illuminate\Support\Facades\Route;

/*
|--------------------------------------------------------------------------
| Web Routes
|--------------------------------------------------------------------------
|
| Here is where you can register web routes for your application. These
| routes are loaded by the RouteServiceProvider within a group which
| contains the "web" middleware group. Now create something great!
|
*/

Route::get('/', function () {
    return view('welcome');
});

```

<br>
use文により、外部ファイルからクラスやメソッド（関数）を引用する。  
<br>

外部から引用してきた Route クラスの get()メソッドを呼び出す記述  
`Route::get( ルートパス , 実行する処理);`  
get()メソッドで GET リクエストに対してルーティングを設定

```
Route::get('/', function () {
    return view('welcome');
});

```

<br>
|リクエスト|	ルートパス|	行われる処理|
|---------|-------------|------------|
|GET|	/|	resources/viewsディレクトリにあるwelcome.blade.phpを表示する|

ルートパスである http://localhost/ にアクセスすると、  
welcome ページを開くことが出来る。

※ルーティングの記述が膨大になるため、  
基本的にコントローラを用いて処理を記述することが多い。

## パラメータについて

ルーティングでは、アクセスする際にパラメータを受け取ることが出来る。

1. パラメータを受け取る（ルーティング）
2. パラメータをコントローラに渡す
3. パラメータをビューに渡す
   <br>

ルーティングで受け取れるパラメータ

- パスパラメータ
- クエリパラメータ

<br>

## パスパラメータ

ルーティングで受け取ったパスパラメータをコントローラに渡し、  
ビューで表示するまでの手順。  
<br>

web.php

```
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\TestController;

- Route::get('/test', [TestController::class, 'index']);

+ Route::get('/test/{text}', [TestController::class, 'index']);
```

アドレス部分に{パラメータ名}を指定し、受け取るルーティングを設定。  
<br>

TestController.php

```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class TestController extends Controller
{
    public function index($text){ /// ・・・①
        $item = [
            'content' => 'パラメータを渡す',
            'param' => $text  /// ・・・②
        ];
        return view('index', $item);
    }
}
```

① でパスパラメータを受け取る。  
index アクションに引数$textを指定。$text にパスパラメータの値が格納される。  
② でビューに渡す連想配列にパスパラメータの値を含める。  
<br>
index.php

```
<body>
    <h1>ブラウザに画面を表示できた！</h1>
    <p><?php echo $content ?></p>

    <table>
        <tr>
            <th>パスパラメータ</th>
            <td><?php echo $param ?></td>
        </tr>
    </table>
</body>
```

ローカルサーバを立ち上げた状態で http://localhost/test/hello にアクセスすると、  
hello の部分がパラメータとして取り出されて表示される。  
<br>

### 必須パラメータ

上記のようなパスパラメータは記述せずにアクセスするとエラーになるため、  
必須パラメータと呼ばれる。  
パラメータなしで http://localhost/test にアクセスすると、  
「ページが存在しない」というエラーになる。  
<br>

### 任意パラメータ

パラメータをつけなくてもアクセス可能になる  
<br>

web.php

```
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\TestController;
use App\Http\Controllers\HelloController;

- Route::get('/test/{text}', [TestController::class, 'index']);
+ Route::get('/test/{text?}', [TestController::class, 'index']);
Route::get('/hello', [HelloController::class, 'index']);
```

<br>

TestController.php

```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class TestController extends Controller
{
    public function index($text = "デフォルト")
    {
            $item = [
                'content' => 'パラメータを渡す',
                'param' => $text
            ];
            return view('index', $item);
        }
}
```

パスパラメータが入力されていないときの為にデフォルト値を設定している。  
任意であるため、パラメータを指定することも可能。  
`http://localhost/test/好きな文字`

<br>

## クエリパラメータ

「クエリパラメータ」＝パラメータを送るために URL の末尾に付け足す文字列

パスパラメータと違い、パラメータを受け取るためのルーティングの設定をする必要がない。

「?」を URL の末尾に付け、「パラメータ＝値」の形で値を渡す。  
複数のパラメータをつけたい場合は「＆」を使ってつなげる。  
`http://localhost/test/?text=56789`

<br>

TestController

```
  <?php

  namespace App\Http\Controllers;

  use Illuminate\Http\Request;

  class TestController extends Controller
  {
          public function index(Request $request)
      {
          $item = [
              'content' => 'パラメータを渡す',
              'param' => $request->text
          ];
          return view('index', $item);
      }
  }
```

クエリパラメータで値を受け取る為に、index の引数に`Request $request`と設定。  
`$request`の中のパラメータをシングルアロー演算子（->）で取り出す。  
<br>

web.php

```
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\TestController;
use App\Http\Controllers\HelloController;

- Route::get('/test/{text?}', [TestController::class, 'index']);
+ Route::get('/test', [TestController::class, 'index']);
```

パラメータを受け取る為のルーティングを設定する必要がないため、web.php を修正。

コントローラでパラメータの値を`$request->text`で取得しているため、URL のクエリ文字列でキーに text を指定する必要がある。  
`test?text=5678`にアクセスすると、`$request->text`により、5678 をパラメータとして受け取ることが出来る。  
<br>

### ルートプレフィックス

prefix メソッドを使用すると、単一のルートやグループ内の各ルートに  
特定の URL をプレフィックスとしてつけることが出来る。  
<br>
例）

```
Route::get('user', [UserController::class, 'index']);
Route::get('user/add', [UserController::class,'add']);
Route::get('user/del', [UserController::class,'del']);
```

上記ではすべてのパスが user という共通のルーティングを経由して、ルーティングが設定されている。  
共通部分を prefix メソッドを用いて簡略化することが出来る。

```
Route::prefix('user')->group(function () {
  Route::get('', [UserController::class, 'index']);
  Route::get('add', [UserController::class,'add']);
  Route::get('del', [UserController::class,'del']);
});
```

<br>

### ルートグループ

prefix やミドルウェアをまとめて一括で定義することが出来る

```
Route::group(['prefix' => 'user'], function() {
  Route::get('', [UserController::class, 'index']);
  Route::get('add', [UserController::class,'add']);
  Route::get('del', [UserController::class,'del']);
});
```

<br>

複数のグループを定義するときはネスト構造にする

```
Route::group(['prefix' => 'user'], function() {
  Route::group(['middleware' => 'auth'],function() {
    Route::get('', [UserController::class, 'index']);
    Route::get('add', [UserController::class,'add']);
    Route::get('del', [UserController::class,'del']);
  });
});
```

3 つの Route::get に対して prefix で user という共通のルーティング、auth というミドルウェアが適用されている。

<br><br>

# ブラウザに画面を表示する：view 編

## ビュー

Controller からの命令や Model から抽出されたデータを画面に表示するプログラム

テンプレートエンジンという仕組みを活用して HTTP レスポンスに応じて HTML を生成する。

Laravel では Blade や PHP テンプレートと呼ばれるテンプレートエンジンを利用する。

「PHP テンプレート」＝ resources/views 直下の index.php など  
<br>

## Blade

Laravel のテンプレートエンジン  
「.blade.php」ファイル拡張子を使用し、「resources>views」ディレクトリに保存  
PHP テンプレートと同じく View の役割を果たす  
<br>

## Blade の作成

1. blade ファイルを作成する
2. コントローラからビューを呼び出す
3. ルーティングを設定する

### blade ファイルを作成する

resources>views ディレクトリに index.blade.php を作成

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
</body>

</html>
```

二重波括弧「{{}}」は PHP の echo と同じ働きをする  
<br>

### コントローラからビューを呼び出す

TestController.php の index アクションでビューを呼び出す処理を記述

```

public function index(){
        $item = [
            'content' => '本文'
        ];
       return view('index', $item);
}
```

※view メソッドは index.php と index.blade.php の両方がある場合は、Blade を優先して読み込む。  
<br>

### ルーティングを設定する

/にアクセスしたときに TestController の index アクションが呼び出されるルーティングを設定

web.php

```

<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\TestController;

- Route::get('/test', [TestController::class, 'index']);
+ Route::get('/', [TestController::class, 'index']);
```

<br>

## Blade の基本

- 変数の表示　`{{$content}}`
- コメントアウト　`{{-- コメント --}}`
- PHP

```
@php
// PHPの処理
@endphp
```

<br>

## 条件分岐

Blade の中には「ディレクティブ」と呼ばれる文法のような機能がある  
<br>

### @if ディレクティブ

if 文と同じように条件分岐を作ることが出来る

```
@if (条件)
// 条件がtrueの時の処理
@else
// 条件がfalseの時の処理
@endif
```

<br>

### @unless ディレクティブ

if 文と反対の処理を行うことが出来る

```
@unless (条件)
//条件が偽のときの処理
@else
//条件が真のときの処理
@endunless
```

<br>

### @switch ディレクティブ

switch 文と同じように条件分岐を作ることが出来る

```
@switch (変数)
  @case (0)
  // 0のときの処理@break
  @case (1)
  // 1のときの処理@break
  @default
  // その他のときの処理@endswitch
```

<br>

### @empty ディレクティブ

値が空かどうかを判定することが出来る

```
@empty (変数)
// 変数が0やfalseやnullのときの処理
@else
// 変数が0やfalseやnullでないときの処理
@endempty
```

<br>

### @isset ディレクティブ

変数が定義されているかを判定することが出来る

```
@isset (変数)
// 変数が定義されていてnullでないときの処理
@else
// 変数が定義されていないかnullのときの処理
@endisset
```

<br>

## 繰り返し

### @for ディレクティブ

for 文と同じように繰り返しを作ることが出来る

```
@for(初期値; 条件; 変化式)
// 繰り返す処理
@endfor
```

<br>

### @foreach ディレクティブ

foreach 文と同じように配列を分解することが出来る

```
@foreach(配列 as 変数)
// 繰り返す処理
@endforeach
```

<br>

### @while ディレクティブ

while 文と同じように繰り返しを作ることが出来る

```
@while(条件)
// 繰り返す処理
@endwhile
```

<br>

### @forelse ディレクティブ

foreach 文とほぼ同じだが、変数が繰り返し可能でない時、自動的に@empty ディレクティブ内の処理をする。

```
@forelse (配列 as 変数)
// 配列に値があるときの処理
@empty
// 配列が空のときの処理
@endforelse
```

<br>

### @break ディレクティブと@continue ディレクティブ

- @break ディレクティブ＝繰り返し処理を中断して終了
- @continue ディレクティブ＝処理のスキップ

```
@while(条件)
// 繰り返す処理
  @break
@endwhile
```

<br>

## 表示

- 「継承」＝既にあるテンプレートのレイアウトを引き継いで新しいレイアウトを作ること
- 「セクション」＝継承でページをデザインする際にページ内の要素として活用される区画のこと
  <br>

### @extends ディレクティブ

親レイアウトの内容を子レイアウトに継承する  
`@extends('ディレクトリ名.ファイル名')`  
<br>

### @section ディレクティブ

レイアウトで様々な区画を定義する

```
@section(セクション名)
//セクションの内容
@endsection
```

<br>

### @yield ディレクティブ

「@section」で定義したものをはめ込むためのもの。  
基本的に親レイアウトに記述し、子レイアウトの@section ディレクティブの内容を表示するために使用する。  
`@yield(セクション名)`  
<br>

### @extends,@yield,@section 使用例

親レイアウト(resources>views>layouts>parent.blade.php)

```
<!DOCTYPE html>
<html lang="ja">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>COACHTECH</title>
</head>

<body>
    <h1>@yield('content')</h1>
</body>

</html>
```

<br>

子レイアウト(resources>views>index.blade.php)

```
@extends('layouts.parent')

@section('content')
Hello
@endsection
```

<br>

実際のコードの表示

```
<!DOCTYPE html>
<html lang="ja">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>COACHTECH</title>
</head>

<body>
    <h1>Hello</h1>
</body>

</html>
```

<br>

## コンポーネント

全く違う部品を作り、それをレイアウトに組み込みたい時に用いる。  
例）ヘッダーやフッター等、他のページでも共通して使用される部品。  
<br>

### @component ディレクティブ

component を飛び出す際に利用。  
変数を引き渡したい時は@slot ディレクティブを使用

```
@component('ディレクトリ名.ファイル名')
//コンポーネントの表示内容
@endcomponent
```

<br>

### @include ディレクティブ

外部ファイルの取り込み、component の読み込みが出来る。  
変数がある場合は、第二引数に連想配列として渡す。  
`@include('ディレクトリ名.ファイル名')`  
<br>

### @each ディレクティブ

@foreach と@include を同時に行うもの。  
<br>

### @include,@component,@each 使用例

コンポーネント(resources>views>components>items.blade.php)  
`<li>{{$item}}</li>`  
<br>

レイアウト(resources>views>index.blade.php)

```
<!DOCTYPE html>
<html lang="ja">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>COACHTECH</title>
</head>

<body>

  <ul>
    @include ('components.items', ['item' => 'include'])
  </ul>
  <ul>
    @component ('components.items')
    @slot ('item')
    component
    @endslot
    @endcomponent
  </ul>
  <ul>
    @each ('components.items', ['item1', 'item2'], 'item')
  </ul>
</body>

</html>
```

<br>

実際のコードの表示

```
<!DOCTYPE html>
<html lang="ja">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>COACHTECH</title>
</head>

<body>

  <ul>
    <li>include</li>
  </ul>
  <ul>
    <li>component</li>
  </ul>
  <ul>
    <li>item1</li>
    <li>item2</li>
  </ul>
</body>

</html>
```
