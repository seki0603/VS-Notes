# Stripe決済機能導入フロー
## テーブル設計
決済機能を導入する前提で、商品のテーブルに必要カラムを設定。  
また注文用のテーブルを作成。  

productsテーブル
```
Schema::create('products', function (Blueprint $table) {
    $table->id();
    $table->foreignId('seller_id')->constrained('users')->onDelete('cascade');
    $table->string('name');
    $table->string('brand_name')->nullable();
    $table->unsignedInteger('price');
    $table->tinyInteger('condition');
    $table->text('description');
    $table->string('image_path')->nullable();
    $table->foreignId('buyer_id')->nullable()->constrained('users')->nullOnDelete();
    $table->timestamp('sold_at')->nullable();
    $table->timestamps();
});
```

ordersテーブル
```
Schema::create('orders', function (Blueprint $table) {
    $table->id();
    $table->foreignId('product_id')->constrained()->onDelete('cascade');
    $table->foreignId('buyer_id')->constrained('users')->onDelete('cascade');
    $table->foreignId('seller_id')->constrained('users')->onDelete('cascade');
    $table->unsignedInteger('price');
    $table->enum('payment_method', ['コンビニ支払い', 'カード支払い']);
    $table->enum('payment_status', ['pending', 'paid', 'failed'])->default('pending');
    $table->char('ship_postal_code', 8);
    $table->string('ship_address');
    $table->string('ship_building')->nullable();
    $table->timestamp('ordered_at')->useCurrent();
    $table->timestamp('paid_at')->nullable();
});
```

## Stripe接続設定
### .envにキー追加（サンドボックス用）
Stripeダッシュボード https://dashboard.stripe.com/acct_1S8zFsJLJtyk1e2R/test/dashboardからAPIをコピーして.envに追記
```
STRIPE_KEY=pk_test_*****
STRIPE_SECRET=sk_test_*****
```

### config/services.php編集
return内に追加
```
'stripe' => [
        'secret' => env('STRIPE_SECRET'),
        'key' => env('STRIPE_KEY'),
    ],
```

## Stripeライブラリ導入
PHPコンテナ内で以下コマンド実行  
`composer require stripe/stripe-php`  

## 必要な場合はFormRequest作成
```
public function rules()
    {
        return [
            'payment_method' => ['required', 'in:コンビニ支払い,カード支払い'],
            'ship_postal_code' => ['required', 'regex:/^\d{3}-\d{4}$/'],
            'ship_address' => ['required'],
            'ship_building' => ['nullable']
        ];
    }
```

## コントローラ処理作成
* PurchaseControllerやPaymentControllerを作成
* 購入画面表示と購入処理のアクションを記述
```
use App\Models\Product;
use App\Models\Order;
use App\Http\Requests\AddressRequest;
use App\Http\Requests\PurchaseRequest;
use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Facades\DB;

// 購入画面表示
public function create($item_id)
    {
        $product = Product::findOrFail($item_id);
        $user = Auth::user();

        return view('purchase.index', compact('product', 'user'));
    }

// 購入処理
public function store(PurchaseRequest $request, $item_id)
    {
        $product = Product::findOrFail($item_id);

        if ($product->sold_at) {
            return redirect()->route('items.index');
        }

        DB::transaction(function () use ($request, $product) {
            Order::create([
                'product_id' => $product->id,
                'buyer_id' => Auth::id(),
                'seller_id' => $product->seller_id,
                'price' => $product->price,
                'payment_method' => $request->payment_method,
                'payment_status' => 'pending',
                'ship_postal_code' => $request->ship_postal_code,
                'ship_address' => $request->ship_address,
                'ship_building' => $request->ship_building,
                'ordered_at' => now(),
            ]);

            $product->update([
                'buyer_id' => Auth::id(),
                'sold_at' => now(),
            ]);
        });

        //テスト用
        if (app()->environment('testing')) {
            return redirect()->route('items.index');
        }

        //本番用
        \Stripe\Stripe::setApiKey(config('services.stripe.secret'));
        $session = \Stripe\Checkout\Session::create([
            'payment_method_types' => $request->payment_method === 'カード支払い'
                ? ['card']
                : ['konbini'],
            'line_items' => [[
                'price_data' => [
                    'currency' => 'jpy',
                    'product_data' => [
                        'name' => $product->name,
                    ],
                    'unit_amount' => $product->price,
                ],
                'quantity' => 1,
            ]],
            'mode' => 'payment',
            'success_url' => route('items.index') . '?session_id={CHECKOUT_SESSION_ID}',
            'cancel_url' => route('purchase.create', $product->id),
        ]);

        session()->flash('message', '購入が完了しました');

        return redirect($session->url);
    }
```

## ルーティング設定
```
Route::middleware(['auth', 'verified'])->group(function () {
    Route::get('/purchase/{item_id}', [PurchaseController::class, 'create'])->name('purchase.create');
    Route::post('/purchase/{item_id}', [PurchaseController::class, 'store'])->name('purchase.store');
});
```

## 購入画面のblade作成
```
<form class="content" action="{{ route('purchase.store', $product->id) }}" method="POST" novalidate>
    @csrf
    <div class="select-content">
        <div class="product">
            <img class="product__img" src="{{ asset('storage/'.$product->image_path) }}" alt="商品画像">
            <div class="product__inner">
                <h3 class="product__name">{{ $product->name }}</h3>
                <p class="product__price">
                    <span class="product__price--span">¥</span>
                    {{ number_format($product->price) }}
                </p>
            </div>
        </div>

        <div class="payment">
            <h3 class="payment__title">支払い方法</h3>
            <div class="payment__inner">
                <input type="hidden" name="payment_method" id="paymentMethodValue" value="{{ old('payment_method') }}">
                <div class="payment__selectbox" id="paymentSelectbox">
                    <div class="payment__selected">
                        {{ old('payment_method') ?? '選択してください' }}
                    </div>
                    <ul class="payment__options">
                        <li class="payment__option" data-value="コンビニ支払い">コンビニ支払い</li>
                        <li class="payment__option" data-value="カード支払い">カード支払い</li>
                    </ul>
                </div>
                @error('payment_method')
                <p class="error">{{ $message }}</p>
                @enderror
            </div>
        </div>

        <div class="ship-address">
            <div class="ship-address__header">
                <h3 class="ship-address__title">配送先</h3>
                <a class="ship-address__change" href="{{ route('purchase.address', $product->id) }}">変更する</a>
            </div>
            <div class="ship-address__inner">
                <p class="postal-code">〒{{ session('ship_postal_code', $user->profile->postal_code) }}</p>
                <p class="address">{{ session('ship_address', $user->profile->address) }}</p>
                <p class="building">{{ session('ship_building', $user->profile->building) }}</p>
                @if (session('message'))
                <p class="success">{{ session('message') }}</p>
                @endif
                @error('ship_address')
                <p class="error">{{ $message }}</p>
                @enderror
            </div>

            <input type="hidden" name="ship_postal_code" value="{{ session('ship_postal_code', $user->profile->postal_code) }}">
            <input type="hidden" name="ship_address" value="{{ session('ship_address', $user->profile->address) }}">
            <input type="hidden" name="ship_building" value="{{ session('ship_building', $user->profile->building) }}">
        </div>
    </div>

    <div class="result-content">
        <table class="table">
            <tr class="table__row">
                <th class="table__header">商品代金</th>
                <td class="table__item">
                    <span class="table__item--span">¥</span>
                    {{ number_format($product->price) }}
                </td>
            </tr>
            <tr class="table__row">
                <th class="table__header">支払方法</th>
                <td class="table__item" id="paymentMethodText">未選択</td>
            </tr>
        </table>
        <button class="purchase-btn" type="submit">購入する</button>
    </div>
</form>
```