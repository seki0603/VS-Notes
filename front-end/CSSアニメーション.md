# transitionプロパティを使う
## transitionの役割
CSSで指定された最初の状態から最後の状態までの変化を自動で計算し、アニメーションさせる。

## transition使い方
```
.link {
    color: #2e7c9f;
    transition: opacity .25s, color .25s;
}

.link:hover {
    color: #d15400;
    opacity: 0.5;
}
```
* 「変化前」の状態からtransitionプロパティで指定する
    * hoverの方にだけ指定してもうまくアニメーションにならない。
* 複数のプロパティを指定する場合はカンマで区切る。

## 指定できる値の種類
### transition-delay
変化までの待ち時間  
初期値：0秒

### transition-duration
変化にかかる時間  
初期値：0秒

### transition-property
変化させるプロパティ名  
初期値：all（意図しない動作の元）

### transition-timing-function
変化の仕方  
初期値：ease
* linear...一定の速度で変化
* ease...変化の速度が途中まではだんだん早くなり、途中からだんだん遅くなる
* ease-in...変化の速度がだんだん早くなる
* ease-out...変化の速度がだんだん遅くなる
<br>

最低限、下記の値を指定する形でプロパティを使う。  
`transition: [変化させるプロパティ名] [変化にかかる秒数];`

### ショートハンドによる記述
```
.link {
    color: #2e7c9f;
    transition: opacity .25s 5s;
}

.link:hover {
    opacity: 0.5;
}
```
時間を2種類指定する場合  
`transition: [1 変化にかかる時間] [2 変化までの待ち時間];`  
この順番で指定する（それ以外は順不同）

# @keyframesでアニメーションを設定する
## @keyframesの使い方
```
@keyframes アニメーション名 {
    // 時間の変化に伴って、どのように変化するか定義する。
}
```
* アニメーション名を決める
* 変化の内容を決める
<br>

```
.circle {
    display: inline-block;
    width: 5rem;
    height: 5rem;
    border-radius: 50%;
    background-color: #2e7c9f;
    animation: 1s infinite change-color;
}
```
`animation: 秒数 繰り返す回数 アニメーション;`
<br>

* アニメーションを適用する要素にanimationプロパティを使用
* 一回のアニメーションにかける秒数を指定
* 繰り返しを指定（infinite = 無限ループ）
* @keyframesで定義したアニメーションを呼び出す

## アニメーションの調整
### 始まりと終わりを指定する
```
@keyframes change-color {
    from {
        background-color: #2e7c9f;
    }
    to {
        background-color: #46b2e3;
    }
}
```
* from...アニメーションの始まりの状態
* to...アニメーションの終わりの状態

### 変化率毎に指定する
```
@keyframes change-color {
    0% {
        background-color: #2e7c9f;
    }
    50% {
        background-color: #5bda82;
    }
    100% {
        background-color: #fae866;
    }
}
```
* 0%をアニメーションの始まり、100%を終わりとして、経過率毎の変化を指定する。
* 実行時間の内の何%経過時点かを指定する。

## 無限ループするアニメーションを実装するコツ
```
@keyframes change-color {
    0% {
        background-color: #46b2e3;
    }
    25% {
        background-color: #5bda82;
    }
    50% {
        background-color: #fae866;
    }
    75% {
        background-color: #5bda82;
    }
    100% {
        background-color: #46b2e3;
    }
}
```
* 最初の状態（0% or form）と最後の状態を同じにする（100% or to）
    * 25%経過時点と75%経過時点の状態を同じにする

## animationプロパティに指定できる値の種類
### animation-name
アニメーション名

### animation-duration
アニメーション1回の実行にかかる秒数

### animation-interation-count
アニメーションを繰りかえす回数  
初期値：1  
具体的な回数を数字で指定するか、infinite（無限）を指定する。

### animation-delay
アニメーション実行までの待ち時間

### animation-timing-function
アニメーションの変化の仕方

### 最低限必要な指定
`animation: [アニメーション1回の実行にかかる秒数] [アニメーション名];`

### ショートハンドによる記述
時間を2種類指定する場合  
`animation: [1 実行にかかる秒数] [2 実行までの待ち時間];`  
この順番で指定する（それ以外は順不同）


## 複数のアニメーションを指定する場合
`animation: 1s infinite change-color, 10s infinite change-size;`
* アニメーションごとにカンマで区切って指定する。

