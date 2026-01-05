# Router Linkによるページ移動
aタグによるページ遷移では、全てのファイルをダウンロードしている。  
Router Linkにより、ダウンロード無しでコンポーネントだけを動的に切り替えることができる

## to属性による遷移先設定
```
<template>
  <h1>Vue Router</h1>
  <RouterLink to="/">Home</RouterLink> | <RouterLink to="/about">About</RouterLink>
  <RouterView />
</template>
```
* `to="相対パス"`で遷移先設定
* element上はaタグ扱いになるため、別タブ表示なども可能
* 絶対パスによる外部サイト遷移は設定不可能

<br><br>

# クエリパラメータとハッシュ設定
```
<template>
  <h1>Vue Router</h1>
  <RouterLink :to="'/?lang=ja#title'">Home</RouterLink> |
  <RouterLink :to="{ path: '/about', query: {lang: 'ja'}, hash: '#title' }">About</RouterLink>
  <RouterView />
</template>
```
* vバインドで文字列として設定可能
* vバインドでオブジェクトとしても設定可能

<br><br>

# ルートに名前をつける
## router/index.js
```
const router = createRouter({
  history: createWebHistory(),
  routes: [
    {
      path: '/',
      name: 'home',
      component: HomeView
    },
    {
      path: '/about',
      name: 'about',
      component: AboutView
    }
  ]
})
```
* routesでnameを文字列で指定
  * 同じ名前は使えない
<br>

## App.vue
```
<template>
  <h1>Vue Router</h1>
  <RouterLink :to="{ name: 'home' }">Home</RouterLink> |
  <RouterLink :to="{ name: 'about' }">About</RouterLink>
  <RouterView />
</template>
```
* to属性をvバインドにし、オブジェクトのプロパティとしてnameを指定
<br>