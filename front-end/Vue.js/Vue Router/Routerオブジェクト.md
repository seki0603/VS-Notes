# プログラム上でページ遷移を設定する
```
<script setup>
import { useRouter } from 'vue-router'

const router = useRouter()
function toAbout() {
  router.push({path: '/about'})
  }
</script>

<template>
  <h1>Vue Router</h1>
  <RouterLink :to="'/?lang=ja#title'">Home</RouterLink> |
  <button @click="toAbout">About</button>
  <RouterView />
</template>
```
* useRouterをインポート
* useRouter関数を定数に代入
* useRouterを代入した定数にpushやreplaceを設定
    * push...遷移履歴を残す(ブラウザバックで戻れる)
    * replace...遷移履歴を残さない(ブラウザバックで戻れない)
* 引数に文字列かオブジェクトで遷移先を設定

<br><br>

# $routerの利用
main.jsの`app.use(router)`により、$routerが使用可能。
```
<script setup></script>

<template>
  <h1>Vue Router</h1>
  <RouterLink :to="'/?lang=ja#title'">Home</RouterLink> |
  <button @click="$router.push({ path: '/about' })">About</button>
  <RouterView />
</template>
```
* イベントに`$router.push()`,`$router.replace()`を設定できる
    * インポート等不要
