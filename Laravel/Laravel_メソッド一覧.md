# Laravel メソッド復習まとめ

## 🧩 条件設定

| メソッド | 効果 | 使い方 | 補足 |
| -------- | ---- | ------ | ---- |
| filled | 値が「null」でない場合に true を返す | `$request->filled('カラム名')` | 空文字は true、null のみ false。配列の場合は全て埋まっている必要あり。 |
| whereRaw | SQL 文を直接書ける | `Post::whereRaw("title = :title", ['title' => $title])->get();` | LIKE や計算式など柔軟に指定可。必ずバインド変数で SQL インジェクションを防止。 |
| query | クエリパラメータを取得 | `$request->query('tab')` | GET パラメータ（例：`?tab=mylist`）を取得。`query('tab') = 'mylist'` は誤り。 |
| latest | 作成日降順で取得 | `Product::latest()->get();` | デフォルトは created_at。別カラムを指定する場合は `latest('column')`。 |
| empty | null, 0, "0", false, 空を判定 | `empty($user->address)` | PHP 標準関数。Laravel コレクションでは `$collection->isEmpty()` を使う。 |
| Str::lower | 文字列を小文字に変換 | `Str::lower($request->{Fortify::username()})` | `use Illuminate\Support\Str;` が必要。 |
| sortByDesc | 指定した値を降順に並べ替え | `$users->sortByDesc('age');` | Eloquent コレクション専用。DB レベルでは `orderByDesc()`。 |
| whereNull | 指定カラムが null のレコードを取得 | `->whereNull('deleted_at')` | 対になるメソッドに `whereNotNull()` あり。 |
| diffInMinutes | 2つの日付の差を分単位で取得 | `$b->break_end->diffInMinutes($b->break_start)` | `diff()` は Carbon オブジェクト、`diffInMinutes()` は整数を返す。 |
| lte | Carbonの日付比較（以下） | `$day->lte($endDate)` | 他に `lt`, `gt`, `gte` などもあり。 |
| keyBy | 指定キーで再インデックス化 | `->get()->keyBy('id')` | コレクションのキーを任意のカラムに変換。日付などでグループ化する際に便利。 |
| preg_match | 正規表現一致を検証 | `preg_match('/^[a-z0-9]+$/', $username)` | Laravel のバリデーションでは `regex:` ルールでも同等処理可。 |

---

## 📦 データ取得

| メソッド | 効果 | 使い方 | 補足 |
| -------- | ---- | ------ | ---- |
| with | リレーションテーブルを同時取得 | `Product::with(['category'])->get();` | N+1 問題対策の eager loading。 |
| withCount | リレーションの件数を取得 | `User::withCount(['likes']);` | 条件付き集計も可能。例：`withCount(['likes as today_likes' => fn($q)=>$q->whereDate('created_at',today())])` |
| findOrFail | 該当なしの場合に 404 を返す | `User::findOrFail($id);` | 例外：`ModelNotFoundException`。try-catchで制御も可能。 |
| load | モデル取得後にリレーションを読込 | `$user->load('profile');` | 既に取得済みのモデルに対して追加読み込み。 |
| collect | データをコレクション化 | `$attendances = collect([]);` | 配列やEloquent結果をコレクション操作用に変換。 |
| map | 要素を変換して新しいコレクションを生成 | `$users->map(fn($u)=>$u->name);` | キーは保持される。再インデックスは `values()` を続けて呼ぶ。 |
| sum | コレクション内の値を合計 | `$breaks->sum(fn($b)=>$b->diffInMinutes());` | DB 上で集計する場合は `->sum('column')`。 |
| createFromFormat | 指定フォーマットで Carbon を生成 | `Carbon::createFromFormat('Y-m', $month);` | 不一致フォーマットは例外発生。`Carbon::parse()` との違いを理解。 |
| input | 入力値を取得 | `$month = $request->input('month', now()->format('Y-m'));` | 第二引数でデフォルト値を指定可能。 |
| filter | 条件に合致した要素のみ残す | `collect($data)->filter(fn($v)=>$v > 0);` | false 相当値（null, 0, '', false）は除外される。 |

---

## 🏗 作成

| メソッド | 効果 | 使い方 | 補足 |
| -------- | ---- | ------ | ---- |
| firstOrCreate | 存在しなければ新規作成 | `Attendance::firstOrCreate(['user_id' => Auth::id()]);` | 第二引数で初期値を渡せる。DB のユニーク制約と組み合わせて安全。 |
| sync | 多対多リレーションの更新 | `$product->categories()->sync($request->categories);` | 該当なしは detach。`syncWithoutDetaching()` で既存保持可。 |
| merge | データを追加・上書き | `$request->merge(['team' => 'Mariners']);` | Request に値を追加する際に便利。破壊的変更に注意。 |

---

## ⚙ 登録・更新・削除・挿入など

| メソッド | 効果 | 使い方 | 補足 |
| -------- | ---- | ------ | ---- |
| DB::transaction | 一連の処理を安全に実行 | `DB::transaction(fn()=>User::create([...]))` | 失敗時は自動ロールバック。例外を投げると終了。 |
| copy | オブジェクトの複製を作る | `$start = $currentMonth->copy()->startOfMonth();` | Carbonなどのmutableオブジェクトを安全に操作するために使用。 |
| str_replace | 文字列の置換 | `str_replace('break_start_', '', $key)` | 正規表現不要の単純置換。`preg_replace`はパターン一致用。 |

---

## 💡 よく使うメソッドチェーン例

```php
// 勤怠一覧のデータ加工例
$totalMinutes = $attendances
    ->sortByDesc('work_date')
    ->filter(fn($a) => $a->status === '承認済み')
    ->sum(fn($a) => $a->working_minutes);

// 修正申請作成（トランザクション＋firstOrCreate）
DB::transaction(function () use ($attendance, $request) {
    $correctionRequest = CorrectionRequest::firstOrCreate(
        [
            'attendance_id' => $attendance->id,
            'user_id' => auth()->id(),
        ],
        [
            'work_date' => $attendance->work_date,
            'note' => $request->input('note'),
        ]
    );
});
```

## 参考文献

- Laravel9 Eloquent 便利なクエリビルダ  
   https://qiita.com/tomeito/items/d51ca717ca48786862ec

- findOrFail() の使い方と find() との違い  
   https://qiita.com/tatsu_nomad/items/6ecc4fad2e928f495f0f

* 多対多のリレーションで sync メソッドを使って中間テーブルを更新する  
   https://qiita.com/hinako_n/items/18957b35124c75fc712d

* merge: Request データを追加・上書き for Laravel  
   https://qiita.com/Fell/items/52be15196099f83ecdc7

* 【Laravel】Collection の sortBy と sortByDesc メソッド  
   https://qiita.com/yukachin0414/items/6c18805c7451620bc59d

* Laravel の「map メソッド」について  
  https://qiita.com/fcafe_goto/items/d460b013527eb51b007c

* 『Carbon』でよく使うパターンをまとめてみた【Laravel 向け】  
  https://coinbaby8.com/carbon-laravel.html
