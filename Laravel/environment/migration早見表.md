# マイグレーション型選び 早見表

| 用途                                             | 推奨カラム型                          | 例                                                 |
| ------------------------------------------------ | ------------------------------------- | -------------------------------------------------- |
| ユーザー名・タイトル・カテゴリー名など短い文字列 | `string('name', 長さ)`                | `$table->string('title', 50);`                     |
| 本文・説明・備考など長い文章                     | `text('body')`                        | `$table->text('description');`                     |
| パスワードやメールアドレス                       | `string('email')`                     | `$table->string('password');`                      |
| 真偽値（ON/OFF, 有効/無効, フラグ）              | `boolean('is_active')`                | `$table->boolean('is_admin');`                     |
| 年齢・数量・在庫数など整数（小さめ）             | `integer('count')`                    | `$table->integer('age');`                          |
| 閲覧数・フォロワー数など大きくなる整数           | `bigInteger('views')`                 | `$table->bigInteger('followers');`                 |
| 金額・小数点を含む値（誤差なく正確に扱いたい）   | `decimal('price', 桁数, 小数桁数)`    | `$table->decimal('price', 8, 2);`                  |
| 科学計算などおおまかに小数を扱えばいい           | `float('score', 桁数, 小数桁数)`      | `$table->float('rating', 5, 2);`                   |
| 日付だけ（誕生日など）                           | `date('birthdate')`                   | `$table->date('published_on');`                    |
| 日時（作成日時・更新日時など）                   | `timestamp('created_at')`             | `$table->timestamp('last_login');`                 |
| 自動で増える ID                                  | `id()`                                | `$table->id();`（BIGINT + AUTO_INCREMENT）         |
| 外部キー（他テーブルの ID 参照）                 | `foreignId('user_id')->constrained()` | `$table->foreignId('category_id')->constrained();` |

<br><br>

# リレーション用型

| 用途                     | 推奨カラム型                             | 記述例                                                                                                                                      | 補足                                      |
| ------------------------ | ---------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------- |
| 外部キー（基本形）       | `foreignId()`                            | `$table->foreignId('user_id')->constrained();`                                                                                              | `users.id` を参照、ON DELETE CASCADE 付き |
| 外部キー（参照先を指定） | `foreignId()->constrained('テーブル名')` | `$table->foreignId('category_id')->constrained('categories');`                                                                              | テーブル名を指定してリレーション          |
| 外部キー（制約なし）     | `unsignedBigInteger()`                   | `$table->unsignedBigInteger('user_id');`                                                                                                    | リレーション定義を別で書く場合            |
| 外部キーにユニーク制約   | `foreignId()->unique()`                  | `$table->foreignId('profile_id')->unique()->constrained();`                                                                                 | 1 対 1 の関係を表す                       |
| 中間テーブル（多対多）   | `foreignId()`を 2 つ                     | `php\n$table->foreignId('user_id')->constrained()->onDelete('cascade');\n$table->foreignId('role_id')->constrained()->onDelete('cascade');` | user_role みたいなテーブル用              |
| 自動採番 ID（主キー）    | `id()`                                   | `$table->id();`                                                                                                                             | BIGINT + AUTO_INCREMENT                   |
| 複合主キー               | `primary()`                              | `$table->primary(['user_id', 'role_id']);`                                                                                                  | 中間テーブルで使うことあり                |
