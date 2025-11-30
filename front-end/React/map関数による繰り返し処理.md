# map関数
* コンポーネント(処理)を繰り返す際に利用する
* データの定義と(繰り返し)処理を分離して記述する

<br><br>

## データを定義
```
const ITEMS = [
    {
        href: "データ1-1",
        title: "データ1-2",
        description: "データ1-3",
    },
    {
        href: "データ2-1",
        title: "データ2-2",
        description: "データ2-3",
    },
    {
        href: "データ3-1",
        title: "データ3-2",
        description: "データ3-3",
    },
]
```
* 普遍的なデータを大文字の表記の定数で定義
* 配列で定義

<br><br>

## map関数で繰り返し処理を記述
```
export function Links() {
    <div>
    {ITEMS.map(item => {
        return (
            <a key={item.href} href={item.href}>
                <h3 key={item.title}>{item.title}</h3>
                <p>{item.description}</p>
            </a>
        );
    })}
    </div>
}
```
* `{定数名.map(変数名 => {return(繰り返し処理);})}`で利用
* return内に`{変数名.キー}`で抜き出すデータを記述
    * データを配列化しているため、キーで指定することが可能
* リストの子要素が一意であることを`key={キー}`で定義