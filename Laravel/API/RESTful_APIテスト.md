# RESTクライアント
## Postman利用
前提：ルーティング`Route::apiResource('/v1/rest', RestController::class);`
### index,show,destroy
* メソッド...(GET,DELETE)
* リクエストURL
    * index...`http://localhost/api/v1/rest`
    * show,destroy...`http://localhost:8000/api/v1/rest/{id}`
<br>

Send(送信)後確認
* ステータスコード...200
* Bodyタグ内でデータやメッセージが意図したものになっているか確認

### store,update
* メソッド...(POST,PUT,PATCH)
* リクエストURL
    * store...`http://localhost:8000/api/v1/rest`
    * update...`http://localhost:8000/api/v1/rest/{id}`
* Body形式...raw
* Body形式プルダウン...JSON
* Body中身
```
{
    "カラム名":"データ"
}
```
<br>

Send(送信)後確認
* ステータスコード...201(store),200(update)
* Bodyタグ内でデータやメッセージが意図したものになっているか確認

## Featureテスト
前提：テスト用DB作成、.env.testing設定、テスト用キー生成、phpunit.xml設定が終わっている
<br>

```

// 前提とする処理内容

$response = $this->メソッド('ルート' . id, データ);
$response->assertStatus(ステータスコード);
$response->assertJsonFragment(作成した値);
$this->assertDatabaseHas('テーブル名', 作成した値);
```
* assertStatusでレスポンスが正常か確認
* assertJsonFragmentで作成・更新した値がレスポンスに含まれているか確認
* assertDatabaseHasでDB確認
