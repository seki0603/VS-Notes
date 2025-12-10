# useState
useStateは定義したものに仮想DOM内(Reactが内部に待つメモリ)で状態を持たせることができる
```
┌───────────────┐
│  React内部のメモリ  │ ← state はここに保存される
└───────────────┘
            │ state が更新されると…
            ▼
┌───────────────┐
│   Virtual DOM  │ ← 新しい UI 構造を計算
└───────────────┘
            │ diff計算
            ▼
┌───────────────┐
│   Real DOM     │ ← 必要な部分だけ再描画
└───────────────┘
```
※ DOM = 画面表示のための構造(データを保持しているわけではない)
<br>

## 使い方

```
import { useState } from "react";

function App() {
    const [count, setCount] = useState(0);

    const handleClick = () => {
        setCount(count + 1);
    };

    return (
        <button onClick={handleClick}>+</button>
        <p>{count}</p>
    );
}
```
* useStateをimport
* `const [count, setCount] = useState(0);`で初期値設定
* 無名関数で変更を定義
* タグ内で関数を呼び出し

## Virtual DOM(仮想DOM)
Reactは仮想DOMを監しており、変更があった場合、差分をReal DOM(DOM)に反映する。
    ⇒ ページ全体を再レンダリングするのではなく、差分だけ再描画する。
<br>

* 差分のみレンダリングすることによる高速化
* パフォーマンス向上

<br><br>

# useEffect
```
useEffect(() => {
    console.log("Hello Hooks");
  }, [count]);
```
* 第二引数でイベント発火のタイミングを決めることができる

<br><br>

# useContext
propsでのデータの引き渡しがバケツリレー形式なのに対して、useContextではデータを親コンポーネントから使いたいコンポーネントに対して直接渡すことができる。
<br>

## データを渡すコンポーネント
```
import { createContext, StrictMode } from "react";

const ShincodeContext = createContext(shincodeInfo);

createRoot(document.getElementById("root")).render(
  <ShincodeContext.Provider value={shincodeInfo}>
    <StrictMode>
      <App />
    </StrictMode>
  </ShincodeContext.Provider>
);

export default ShincodeContext;
```
* createContextをインポート(自動補完可能)
* `createContext()`に渡すデータを格納
* データを受け取るコンポーネント全体で利用できるようにProviderでコンポーネントタグを囲う
* `<ShincodeContext.Provider value={}>`のvalueで渡すデータを指定
* `export default`で他のコンポーネントで使えるようにする
<br>

## データを受け取る側のコンポーネント
```
import { useEffect, useState, useContext } from "react";
import ShincodeContext from "./main";

function App() {
  const shincodeInfo = useContext(ShincodeContext);

  return (
    <div className="App">
      <h1>useContext</h1>
      <p>{shincodeInfo.name}</p>
      <p>{shincodeInfo.age}</p>
    </div>
  );
}
```
* useContextをインポート
* `const shincodeInfo = useContext(ShincodeContext);`で定数化
    * 自動補完でShincodeContextがインポートされる
* `インポートした定数.キー`でインポートしたデータが利用可能

<br><br>

# useRef
指定したデータを参照できる
```
import { useEffect, useState, useContext, useRef } from "react";

function App() {
  const ref = useRef();

  const handleRef = () => {
    console.log(ref.current.value);
  };

  return (
    <div className="App">
      <h1>useRef</h1>
      <input type="text" ref={ref} />
      <button onClick={handleRef}>UseRef</button>
    </div>
  );
}
```
* useRefをインポート
* 定数を用意
* 参照元のデータを`ref={}`で指定
* 参照する方法を関数で指定

<br><br>

# useReducer
* Store...データの倉庫
* State...データの状態
* Event Handler...UI操作によって呼びだされるアクション
* Dispatch...アクションが呼び出されたことをStoreに知らせる通知
* Reducer...Storeの中のデータの状態を変化させる
  * Dispatchを受けてStateにEvent Handlerの内容を反映
<br>

```
const reducer = (state, action) => {
  switch (action.type) {
    case "increment":
      return state + 1;
    case "decrement":
      return state - 1;
    default:
      return state;
  }
};

function App() {
  const [state, dispatch] = useReducer(reducer, 0);

  return (
    <div className="App">
      <h1>useReducer</h1>
      <p>カウント: {state}</p>
      <button onClick={() => dispatch({ type: "increment" })}>＋</button>
      <button onClick={() => dispatch({ type: "decrement" })}>ー</button>
    </div>
  );
}
```
* action内容を定義
* `onClick={() => dispatch({ type: "increment" })}`で呼び出し

<br><br>

# useMemo
重たい処理を毎回走らせるのではなく、特定のアクションの時のみ走らせたい場合に使用する。  
値を一度メモリに保存し、メモリから値を取り出して表示する  
<br>

```
function App() {
  const [count01, setCount01] = useState(0);
  const [count02, setCount02] = useState(0);

  // 重たい処理
  const square = useMemo(() => {
    let i = 0;
    while (i < 2000000000) {
      i++;
    }
    return count02 * count02;
  }, [count02]);

  return (
    <div className="App">
      <h1>useMemo</h1>
      <div>カウント１：{count01}</div>
      <div>カウント２：{count02}</div>
      <div>結果：{square}</div>
      <button onClick={() => setCount01(count01 + 1)}>＋</button>
      <button onClick={() => setCount02(count02 + 1)}>＋</button>
    </div>
  );
}
```
* 重たい処理をuseMemoで囲う
  * 第二引数に呼び出す条件を記述
<br>

※全ての処理をuseMemoにするとメモリが重たくなる。一度実装した後、特定の重たい処理が発生した場合、パフォーマンス向上の為にその処理だけuseMemoにするとよい。

<br><br>

# useCallback
useMemoの関数バージョン  
値ではなく、関数をメモリに保存する
```
const [counter, setCounter] = useState(0);

  const showCount = useCallback(() => {
    alert('これは重い処理です。');
  }, [counter]);
```
* 特定の関数をuseCallbackで囲う

<br><br>

# カスタムフック
useを自作できる  
例）ローカルストレージに保存された値をボタンクリックで変更する場合

## 親コンポーネント
```
import { useEffect, useState, useContext, useRef, useReducer, useMemo, useCallback } from "react";
import useLocalStorage from "./useLocalStorage";

const [age, setAge] = useLocalStorage("age", 24);

function App() {
  const [age, setAge] = useLocalStorage("age", 24);

  return (
    <div className="App">
      <h1>カスタムフック</h1>
      <p>{age}</p>
      <button onClick={() => setAge(80)}>年齢をセット</button>
    </div>
  );
}

export default App;
```

## 子コンポーネント
```
import React, { useEffect, useState } from "react";

const useLocalStorage = (key, defaultValue) => {
  const [value, setValue] = useState(() => {
    const jsonValue = window.localStorage.getItem(key);
    if (jsonValue !== null) return JSON.parse(jsonValue);

    return defaultValue;
  });

  useEffect(() => {
    window.localStorage.setItem(key, JSON.stringify(value));
  }, [value, setValue]);

  return [value, setValue];
};

export default useLocalStorage;
```