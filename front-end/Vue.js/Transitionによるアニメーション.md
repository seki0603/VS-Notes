# 要素が出たり消えたりする時のアニメーション
```
<script setup>
import { ref } from 'vue'

const isShow = ref(true)
</script>

<template>
  <h1>Animation</h1>
  <button @click="isShow = !isShow">switch</button>
  <Transition>
    <div v-if="isShow">Hello</div>
  </Transition>
</template>

<style scoped>
.v-enter-from {
  opacity: 0;
}
.v-enter-active {
  transition: opacity 1s;
}
.v-enter-to {
  opacity: 1;
}
.v-leave-from {
  opacity: 1;
}
.v-leave-active {
  transition: opacity 1s;
}
.v-leave-to {
  opacity: 0;
}
</style>
```
* .v-enter-from...要素が現れるときの最初の状態
* .v-enter-active...enter-fromからenter-toまでのアニメーション
* .v-enter-to...要素が現れるときの最後の状態
* .v-leave-from...要素が消えるときの最後の状態
* .v-leave-active...leave-fromからleave-toまでのアニメーション
* .v-leave-to...要素が消えるときの最後の状態

<br><br>

# Transitionコンポーネント
## <Transition>の規定
* <Transition>の中は一つの要素にまとまっている必要がある
* コンポーネントを読み込む場合、コンポーネントが一つの要素にまとまっている必要がある。
* v-ifとv-elseで表示が常に一つの要素になる場合は、複数要素での可能。
<br>

## 複数のTransitionコンポーネントの利用
`<Transition name="名前">`
* name属性の利用により、複数のTransitionを区別できる
* CSSプロパティは`.名前-enter-from`になる
<br>

## CSSアニメーションの利用
```
<script setup>
import { ref } from 'vue'

const isShow = ref(true)
</script>

<template>
  <h1>Animation</h1>
  <button @click="isShow = !isShow">switch</button>
  <Transition name="slide">
    <div v-if="isShow">Hello slide</div>
  </Transition>
</template>

<style scoped>
.slide-enter-active {
  animation: slide 1s;
}
.slide-leave-active {
  animation: slide 1s reverse;
}

@keyframes slide {
  0% {
    transform: translateX(20px);
  }
  100% {
    transform: translateX(0);
  }
}
</style>
```
* `@keyframes`でアニメーションを定義
* enter-activeとleave-activeを定義

<br><br>

# 最初のロード時にアニメーションをつける
`<Transition name="fade" appear>`

* appearをつけるとロード時にenterで定義したアニメーションが適用される。

<br><br>

# 要素と要素の切り替え時にアニメーションをつける
```
<script setup>
import { ref } from 'vue'

const isShow = ref(true)
</script>

<template>
  <h1>Animation</h1>
  <button @click="isShow = !isShow">switch</button>
  <Transition name="fade" mode="out-in">
    <div v-if="isShow">ON</div>
    <div v-else>OFF</div>
  </Transition>
</template>

<style scoped>
.fade-enter-from {
  opacity: 0;
}

// 以下略
</style>
```
* `mode="out-in"`により、一つ目の要素が消えるアニメーションが終わってから、二つ目の要素が出るときのアニメーションが始まるようになる。
* modeを指定する際、Transitionコンポーネントの中身は別コンポーネントに切り出さず、直接記述されている必要がある。
    * slotとの併用は可能
