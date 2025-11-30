# API利用手順
```
const apiKey = "9eb187317d107d505bc8a1fb025d2849";
const apiUrl = "https://api.openweathermap.org/data/2.5/weather?units=metric&q=";

async function checkWeather(city) {
    const response = await fetch(`${apiUrl}${city}&appid=${apiKey}`);

    // 中略

    const data = await response.json();

    document.querySelector(".city").innerHTML = data.name;

    // 以下略
}
```

* apiキーを定数に代入
* apiのURLを定数に代入
* `async function 関数名()`でapiを利用する関数を定義
* `const response = await fetch(`${apiUrl}（${入力値}）&appid=${apiKey}`);`で取得したapi情報を定数に格納
* `var data = await response.json();`でレスポンスからJSONデータを非同期に読み込み
* 取得したapiのデータから利用する値をキー指定で取得

# fetch/await
fetch() は Promise を返す非同期処理。await で待つことで「response が返るまで次に進まない」ようにしている。

## 処理の流れ
fetch() → Response オブジェクト → response.json() → JSオブジェクトとして利用
