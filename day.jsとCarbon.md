# day.js と Carbon の format（表示形式）

## 値の指定方法

| 項目     | day.js                       | Carbon                         |
| -------- | ---------------------------- | ------------------------------ |
| 基本構文 | dayjs().format("YYYY-MM-DD") | Carbon::now()->format('Y-m-d') |
| 年       | YYYY                         | Y                              |
| 月       | MM                           | m                              |
| 日       | DD                           | d                              |
| 時       | HH                           | H                              |
| 分       | mm                           | i                              |
| 秒       | ss                           | s                              |

## 便利なフォーマット例

| 表示したい内容  | **day.js**（JavaScript）      | **Carbon**（PHP）                | 出力例                       |
| --------------- | ----------------------------- | -------------------------------- | ---------------------------- |
| 年（4 桁）      | `YYYY`                        | `Y`                              | `2025`                       |
| 年（2 桁）      | `YY`                          | `y`                              | `25`                         |
| 月（2 桁）      | `MM`                          | `m`                              | `10`                         |
| 月（1 桁）      | `M`                           | `n`                              | `10`                         |
| 日（2 桁）      | `DD`                          | `d`                              | `22`                         |
| 日（1 桁）      | `D`                           | `j`                              | `22`                         |
| 曜日（略称）    | `ddd`                         | `D`（Carbon は`locale()`依存）   | `金曜日`                     |
| 曜日（完全）    | `dddd`                        | `l`（L の小文字）                | `金曜日`                     |
| 午前/午後       | `A`                           | `A`                              | `午後`                       |
| 時（24h・2 桁） | `HH`                          | `H`                              | `14`                         |
| 時（12h・2 桁） | `hh`                          | `h`                              | `02`                         |
| 分（2 桁）      | `mm`                          | `i`                              | `30`                         |
| 秒（2 桁）      | `ss`                          | `s`                              | `45`                         |
| ミリ秒          | `SSS`                         | `v`（PHP8.2〜）または`u`（6 桁） | `123`                        |
| 日付と時刻      | `YYYY-MM-DD HH:mm:ss`         | `Y-m-d H:i:s`                    | `2025-10-22 14:30:00`        |
| 日本語形式      | `YYYY年MM月DD日（ddd） HH:mm` | `Y年m月d日（D） H:i`             | `2025年10月22日（水曜日） 14:30` |

## 日本で最もよく見る（日）形式の曜日の出し方

### day.js

```
const dayNames = ["日", "月", "火", "水", "木", "金", "土"];
const now = dayjs();

const displayTime = `${now.format('YYYY年MM月DD日')}(${dayNames[now.day()]}) ${now.format('HH:mm:ss')}`;
console.log(displayTime);
```

もしくは

```
const day = dayjs().day();
const dayT = ["(日)", "(月)", "(火)", "(水)", "(木)", "(金)", "(土)"];

const today = dayjs().format('YYYY年MM月DD日');
const time = dayjs().format("HH:mm:ss");

const displayTime = today + dayT[day] + time;
console.log(displayTime);
```

### Carbon

```
$week = ['日', '月', '火', '水', '木', '金', '土'];
$today = Carbon::now();
echo $today->format("Y年m月d日") . "(" . $week[$today->dayOfWeek] . ")";
```

※「曜日略 1 文字＋括弧」は日本だけの文化圏使用のため、ライブラリ側はサポート外。配列自作が必要。
