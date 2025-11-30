# UI の構成

## 従来の開発

- 1 つのファイル（index.html）に Header,Sidebar,Main,Footer を全て記述
- 他のファイルにも Header...などを全て記述

## React での開発

- Root(\_app.js)
  _ Header.js
  _ Nav.js
  _ Logo.js
  _ Main.js
  _ Sidebar.js
  _ Content.js
  _ Footer.js
  _ Copyright.js
  <br>

- コンポーネントごとに分けて js/jsx ファイルを用意
- コンポーネントを組み合わせて UI を作っていく
- 基本的には１ファイル＝１コンポーネント
  - １ファイルに数コンポーネント書くことも出来る

# App Router

- フォルダ毎にルートが作られる
- 拡張子に関わらず、page.(js|jsx|ts|tsx)がルーティングのエンドポイントになる

```
app/
  page.tsx       → "/"
  about/
    page.tsx     → "/about"
  contact/
    page.js      → "/contact"
  blog/
    page.tsx     → "/blog"
```

# コンポーネントの呼び出し

## コンポーネント用ディレクトリ作成

project/components

## コンポーネントファイル作成・記述

components/Footer.js

```
import Image from "next/image";

export function Footer() {
return (

    // 表示内容

    );
}
```

- `export function コンポーネント名()`でコンポーネントを定義

## インポートと呼び出し

```
import { Footer } from "@/components/Footer";

export default function Home() {
  return (

    // 中略

    </div>
        <Footer />
      </main>
    </div>
  );
}
```

- `import { コンポーネント名 } from "@パス";`でコンポーネントを読み込み
- `<コンポーネント名 />`で呼び出し
