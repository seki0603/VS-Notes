# Vue Routerとは
URLごとに違うコンポーネント(ページ)を表示するための設定

<br><br>

# Vue Routerの設定
## インストール
`npm install vue-router@4`
* create時の設定でインストールすることも可能
<br>

## パスに対応するファイルの用意
一例
* src/views/HomeView.vue
* src/views/AboutView.vue
<br>

## ルーティングの設定ファイルを用意
src/router/index.js
```
import { createRouter, createWebHistory } from 'vue-router'
import HomeView from './views/HomeView.vue'
import AboutView from './views/AboutView.vue'

const router = createRouter({
  history: createWebHistory(),
  routes: [
    {
      path: '/',
      component: HomeView
    },
    {
      path: '/about',
      component: AboutView
    }
  ]
})
export default router
```
* `createRouter`,`createWebHistory`をインポート
* URLに対応させるvueファイルをインポート
* エクスポートするために定数に代入する形で`createRouter`を設定
* 設定を代入した定数をエクスポート
<br>

### createRouterの設定
```
const router = createRouter({
  history: createWebHistory(),
  routes: [
    {
      path: '/',
      component: HomeView
    },
    {
      path: '/about',
      component: AboutView
    }
  ]
})
```
* history...履歴管理の設定。基本的に`createWebHistory`を指定
* routes...pathとcomponentでどのURLにどのコンポーネントが対応するかを設定
<br>

## main.jsの設定
```
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'

const app = createApp(App)

app.use(router)
app.mount('#app')
```
* ルーティングを設定したファイルをインポート
* `app.use(router)`を記述
<br>

## URLによって表示を切り替える部分の設定
App.vue
```
<script setup></script>

<template>
  <h1>Vue Router</h1>
  <RouterView />
</template>
```
* template内の動的に切り替える部分に`<RouterView />`を記述
    * URLによって`<RouterView />`の部分の表示が変わる