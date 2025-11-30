# Linkコンポーネントによる画面遷移
## Linkコンポーネントの読み込み
`import Link from "next/link";`

## 画面遷移設定
```
export function Header() {
  return (
    <header>
      <Link href="/">
        <a>Index</a>
      </Link>
      <Link href="/about">
        <a>About</a>
      </Link>
    </header>
  );
}
```

* aタグをLinkタグで挟む
* Linkタグ内に`href=""`で遷移先指定

# aタグでの遷移とLinkコンポーネント遷移の違い
* aタグ...ブラウザが完全にリロードし直す
* Linkコンポーネント...必要な分だけ読み込む
<br>

読み込み速度に差が出る

## prefetch
※本番環境でのみ有効になる

* 画面内に映ったリンク先のデータを予め取得する
    * バックグランド側で取得する
<br>

さらに読み込み速度が高速化する

# スタイルの当て方
```
<Link href="/">
  <a className={classes.anchor}>Index</a>
</Link>
```

* スタイルはLinkタグに当てず、子要素のaタグに当てる