# onClickでイベントを定義
## タグ内に記述
```
<a href="/about" onClick={function () {
    // イベント内容
}}>アンカーテキスト</a>
```
* タグ内に`onClick={function () {}}`を記述

<br>

## export(コンポーネント)内に定義
```
export default function Home() {
    function handleClick() {
        // イベント内容
    }

    return {
        <a href="/about" onClick={handleClick}>
    }
}
```
* return外に関数を定義し、タグ内で`onClick={関数名}`で呼び出し
※再レンダリングの際にメソッドも再生成される→パフォーマンスが落ちる
<br>

### useCallbackの利用
```
import { useCallback } from "react";

export default function Home() {
    function handleClick = useCallback((e) => {
        // イベント内容
    }, []);

    return {
        <a href="/about" onClick={handleClick}>
    }
}
```
* 再レンダリングされた時にメソッドが再生成されなくなる
* 第二引数が必要

<br>

## export(コンポーネント)外に記述
```
function handleClick() {
        // イベント内容
}

export default function Home() {
    return {
        <a href="/about" onClick={handleClick}>
    }
}
```


<br><br>

# イベントの属性
```
<a href="/about" onClick={function (e) {
    console.log(e.target.href);
    e.preventDefault();
}}>アンカーテキスト</a>
```
* e.target.○○...クリックした要素の属性にアクセス