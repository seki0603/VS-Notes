# 前提条件をできるだけ固定
* ダミーデータのための条件付きFactoryの利用は出来るだけ避ける。
* テスト内create
* 時間を扱う場合はCarbon::setTestNow()で現在時刻を固定。

<br><br>

# 時間を扱う場合のテスト
## tests/TestCase.phpでタイムゾーン指定
```
abstract class TestCase extends BaseTestCase
{
    use CreatesApplication;

    protected function setUp(): void
    {
        parent::setUp();
        date_default_timezone_set('Asia/Tokyo');
        Carbon::setLocale('ja');
    }
}
```

* アプリ側でタイムゾーンをAsia/Tokyoに指定しても、テスト内は指定が無い限りUTC扱いになる。

<br><br>

## テストメソッド毎にCarbon::setTestNow()
```
/** @test */
    public function 出勤は一日一回のみできる()
    {
        Carbon::setTestNow(Carbon::create(2025, 10, 6, 18, 0, 0));

        /** @var User $user */
        $user = User::factory()->create();

        Attendance::create([
            'user_id' => $user->id,
            'work_date' => now()->toDateString(),
            'clock_in' => now()->subHours(8),
            'clock_out' => now(),
        ]);

        $this->actingAs($user)
            ->get('/attendance')
            ->assertDontSee('出勤');
    }
```

* add・subはsetTestNowで設定した時間に対して適用される。

<br><br>

## 時間経過に伴う操作を定義する場合は、操作ごとにCarbon::setTestNow()
```
/** @test */
    public function 休憩時刻が勤怠一覧画面で確認できる()
    {
        Carbon::setTestNow(Carbon::create(2025, 10, 6, 9, 0, 0));

        /** @var User $user */
        $user = User::factory()->create();

        $attendance = Attendance::create([
            'user_id' => $user->id,
            'work_date' => '2025-10-06',
            'clock_in' => now(),
            'clock_out' => null,
        ]);

        Carbon::setTestNow(Carbon::create(2025, 10, 6, 10, 0, 0));
        $this->actingAs($user)->post(route('attendance.store'), [
            'break_start' => now()->format('H:i'),
        ]);
        Carbon::setTestNow(Carbon::create(2025, 10, 6, 10, 15, 0));
        $this->actingAs($user)->post(route('attendance.store'), [
            'break_end' => now()->format('H:i'),
        ]);

        $attendance->refresh();

        $response = $this->actingAs($user)->get('/attendance/list');

        $response->assertSee('0:15');
        // Figmaによる画面設計に従い、時刻ではなく合計休憩時間表示で確認。
    }
```

* createする時間をずらして、時間経過を再現。

<br><br>

# テスト用コマンド
## Featureテストをすべて実行
* php artisan test --testsuite=Feature
* vendor/bin/phpunit --testsuite=Feature

## 全テスト（Unit + Feature）実行
* php artisan test
* vendor/bin/phpunit

## 特定のファイルだけ実行
* php artisan test tests/Feature/ファイル名.php
* vendor/bin/phpunit tests/Feature/ファイル名.php

## 特定のメソッド名だけ実行
* php artisan test --filter=メソッド名
* vendor/bin/phpunit --filter=メソッド名

## 静かに結果だけ表示（簡潔表示）
* php artisan test -q
* vendor/bin/phpunit --quiet

## 詳細ログ付きで実行（デバッグ用）
* php artisan test -v
* vendor/bin/phpunit --verbose

## テスト結果のカバレッジ表示（要：Xdebug）
* php artisan test --coverage
* vendor/bin/phpunit --coverage-html coverage/

## Featureディレクトリ内すべてを再帰実行
* php artisan test tests/Feature
* vendor/bin/phpunit tests/Feature

<br>

# chatGPTより
## DBリセットの推奨設定

* すべての Feature テストで use DatabaseMigrations; を利用する。
* Seeder の併用は極力避け、テストごとに create() で明示的にデータを用意。
<br>

## ルート指定

ルートはハードコードせず、route('attendance.detail', ['id' => $attendance->id]) の形で呼び出す。

⇒ リファクタ時にルート変更があってもテストが壊れない。
<br>

## テストメソッドの命名

「条件 + 期待結果」で表現する。

`public function 出勤時間が退勤時間より後の場合_エラーメッセージを表示()`
<br>

## assert 系の注意点

assertSee() の第2引数に false を渡すと、HTMLエスケープを無効化できる。

`$response->assertSee('出勤中', false);`

改行やスペースの差で落ちるケースに有効。
<br>

## ダミーデータの再現性

sequence() を使って、日付や状態を固定化する。
```
->sequence(
    ['status' => '承認待ち'],
    ['status' => '承認済み'],
    ['status' => '却下']
)
```

random() の使用は避ける（テスト再現性が崩れるため）。
<br>

## テスト環境設定（.env.testing）
```
APP_ENV=testing
APP_DEBUG=true
DB_DATABASE=testing_db
CACHE_DRIVER=array
SESSION_DRIVER=array
MAIL_MAILER=array
```

外部送信やキャッシュの副作用を防止できる。