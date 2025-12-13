# ファイルの役割

## index.html

最終的にブラウザに表示されるファイル

## main.js

Vue アプリの初期化・エントリーポイント

```
import './assets/main.css'

import { createApp } from 'vue'
import App from './App.vue'

createApp(App).mount('#app')
```
* `createApp(App)`...ルートコンポーネント(一番外枠)を指定
* `mount('#app')`...index.htmlの`<div id="app"></div>`に紐づけ

## App.vue
Vueアプリのエントリーポイント。他のコンポーネントを含み、アプリ全体のレイアウトなどを決める。  
templateタグの中身は一つのタグにまとまっている必要がある。

<br><br>

# Vueファイルの中身
* script部分...(JavaScript)
* template部分...(HTML)
* style部分...(CSS)
に分けることができる  
※最低限template部分があれば、コンポーネントとして使用可能。

## script部分
```
<script setup>
import HelloWorld from './components/HelloWorld.vue'
import TheWelcome from './components/TheWelcome.vue'
</script>
```
* 使用するコンポーネントのインポート

## template部分
index.htmlにマウントされ、画面に表示されるHTMLを記述
```
<template>
  <header>
    <img alt="Vue logo" class="logo" src="./assets/logo.svg" width="125" height="125" />

    <div class="wrapper">
      <HelloWorld msg="You did it!" />
    </div>
  </header>

  <main>
    <TheWelcome />
  </main>
</template>
```
* script部分でインポートしたコンポーネントを使用

## style部分
```
<style scoped>
header {
  line-height: 1.5;
}

// 以下略
</style>
```
* CSS記述

# components配下
コンポーネントファイルが入っている

# assets配下
画像やCSSファイルが入っている