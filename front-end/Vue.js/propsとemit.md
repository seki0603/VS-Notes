# 親コンポーネントから子コンポーネントにデータを渡す

App.vue(親側)
```
<script setup>
import { ref } from 'vue'
import ShowCount from './components/ShowCount.vue'
const count = ref(0)
</script>

<template>
  <ShowCount :foo="count" />
  <button @click="count++">+1</button>
</template>
```
* script内で渡すデータを定義
* template内でコンポーネントの属性として渡すデータを記述
    * v-bindで定義しない場合は文字列として扱われる
<br>

ShowCount.vue(子側)
```
<script setup>
defineProps(['foo'])
</script>

<template>
  <p>count: {{ foo }}</p>
</template>
```
* script内でdefineProps関数を使ってデータを受け取る
* 受け取ったデータをtemplate内で利用

<br>

## バリデーション
ShowCount.vue(子側)
```
<script setup>
defineProps({
  foo: {
    type: Number,
    required: true,
  }
})
</script>

<template>
  <p>count: {{ foo }}</p>
</template>
```
* `defineProps({})`でオブジェクトとしてバリデーションを定義
* requiredとdefault設定のどちらかを定義することが推奨される
  * requiredとdefaultを両方同時に定義することはできない
  * requiredはデフォルトでfalse(nullable)になっている
<br>

## より詳細なバリデーション
ShowCount.vue(子側)
```
<script setup>
defineProps({
  foo: {
    type: Number,
    required: true,
    validator(value) {
      return value === 0 || value === 1
    }
  }
})
</script>
```
* validatorメソッドを用いて関数で詳細なバリデーションを設定できる。
  * 引数にdefinePropsで受け取った値が格納される
  * コンソール画面でバリデーションエラーを確認可能

<br><br>

# 子コンポーネントからイベントと共に親にデータを渡す
ResetButton.vue(子側)
```
<script setup>
defineEmits(['reset'])
</script>

<template>
  <button @click="$emit('reset', 100)">Reset</button>
</template>
```
* 発生させるイベント名を`defineEmits(['イベント名'])`で定義
* `@イベント発火="$emit('イベント名', 値)"`でイベントの呼び出しを定義
  * 第二引数にイベントと共に渡すデータを定義(任意)
<br>

App.vue(親側)
```
<script setup>
import { ref } from 'vue'
import ResetButton from './components/ResetButton.vue';

const count = ref(0)
function onReset(value) {
  count.value = value
}
</script>

<template>
  <p>count: {{ count }}</p>
  <button @click="count++">+1</button>
  <ResetButton @reset="onReset" />
</template>
```
* 属性として`@イベント名="イベントの内容"`の形でイベントを受け取る
* イベントの内容を定義
* 第二引数でデータを受け取っている場合、イベント処理の引数に格納される
<br>

## script内でemitを定義する場合
ResetButton.vue(子側)
```
<script setup>
const emit = defineEmits(['reset'])
function emitReset() {
  emit('reset', 100)
}
</script>

<template>
  <button @click="emitReset">Reset</button>
</template>
```