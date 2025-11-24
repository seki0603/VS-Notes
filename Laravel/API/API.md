# API リソースルート

## コントローラ作成

前提：DB 設計・モデル作成が終わっている  
`php artisan make:controller コントローラ名 --api --model=モデル名`

### コントローラ内メソッド

デフォルトでメソッドが用意(指定)されている
|操作| メソッド|
|----|---------|
|一覧表 |index|
|新規作成| store|
|個別表示 |show|
|更新処理| update|
|削除処理 |destroy|

## ルート設定
api.phpにアクションメソッドのルート情報を追記
```
use App\Http\Controllers\リソースコントローラ名;

Route::apiResource('/v1/rest', リソースコントローラ::class);
```

## アクションメソッド作成
```
// 処理内容記述

return response()->json([
  'data' => $items // 返す内容を記述
], 200);　// ステータスコード指定
```

処理の成功と失敗で条件分岐する場合
```
public function update(Request $request, Rest $rest)
    {
        $update = [
          'message' => $request->message,
          'url' => $request->url
        ];
        $item = Rest::where('id', $rest->id)->update($update);
        if ($item) {
            return response()->json([
              'message' => 'Updated successfully',
            ], 200);
        } else {
            return response()->json([
              'message' => 'Not found',
            ], 404);
        }
    }
```
* APIはレスポンスとしてJSON形式のデータを返すためjsonを利用
* 第一引数にメッセージ、第二引数にステータスコードを指定