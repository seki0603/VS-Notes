# ReactiveEffectとは
リアクティブな値を監視し、変更が検知された時に即時反映するシステム。
<br>

* computed...変更を検知したら関数を再実行する
  * 関数の外のあるものの状態を変更することは推奨されていない
  * 処理を1つにまとめるために使用される
* template...変更を検知したらブラウザを再描画する
* watchEffect...変更を検知したら関数を再実行する
  * 関数の外のあるものの状態を変更することができる
  * 関数内で呼ばれたリアクティブな値全てを監視する
* watch...変更を検知したら関数を再実行する
  * 監視対象を第一引数で限定することができる

<br><br>

# computed
リアクティブな状態を保ったまま、処理を1つにまとめる。

## template内に処理を記述する場合
```
<script setup>
import { ref } from 'vue'

const score = ref(0)
</script>

<template>
  <p>{{ score }}</p>
  <p>{{ score > 3 ? 'Good' : 'Bad' }}</p>
  <button @click="score++">+1</button>
</template>
```

## computedを用いてscript内に処理をまとめる場合
```
<script setup>
import { computed, ref } from 'vue'

const score = ref(0)
const evaluation = computed(() => {
  return score.value > 3 ? 'Good' : 'Bad'
})
</script>

<template>
  <p>{{ score }}</p>
  <p>{{ evaluation }}</p>
  <button @click="score++">+1</button>
</template>
```
* 返り値(return)を指定する

## computed利用時の注意点
* computedにデータをセットすることはできない
* 関数の外側にあるものの状態を変更するような処理は推奨されていない
* 非同期の処理を含めない
<br>

## computed利用の利点
* templateがブラウザが一部でも変更された場合、全て再描画するが、computedは関数単位で実行できる
* 監視し始めた値が表示されないときは関数を実行せず、変更があった履歴だけ記録している
* 変更後に表示された場合、記録した履歴を遡って関数実行後の値を反映する

<br><br>

# watchEffect
computedと同じく、リアクティブな値の変更を検知して関数を再実行するが、外側の状態を変更する処理を含めることができる。
<br>

例）
```
<script setup>
import { ref, watchEffect } from 'vue'

const count = ref(0)

watchEffect(() => {
  console.log('watchEffect')
  console.log(count.value)
  count.value = "Hello"
})
</script>

<template>
  <p>{{ count }}</p>
  <button @click="count++">+1</button>
</template>
```
* 監視する対象は、一度関数内で呼ばれたリアクティブな値のみ

<br><br>

# watch
引数を2つ取り、第一引数に監視対象・第二引数に実行する処理を指定できる。  
watchEffectが関数内で呼ばれた全てのリアクティブな値を監視するのに対して、watchは監視対象を限定することができる。
<br>

例）
```
<script setup>
import { ref, watch } from 'vue'

const count1 = ref(0)
const count2 = ref(0)

watch(count1, () => {
  console.log(count1.value, count2.value,)
})

</script>

<template>
  <p>{{ count1 }}</p>
  <p>{{ count2 }}</p>
  <button @click="count1++">+1</button>
  <button @click="count2++">+1</button>
</template>
```
* 上記の場合、変更されるのは第一引数に指定したcount1のみ
<br>

## 変更前・変更後の値の取得
```
<script setup>
import { ref, watch } from 'vue'

const count1 = ref(0)

watch(count, (newValue, oldValue) => {
  console.log('newValue', newValue)
  console.log('oldValue', oldValue)
})
</script>
```
* 関数の引数で変更前・変更後の値を取得することができる

## 第一引数に関数を指定する場合
```
<script setup>
import { ref, watch } from 'vue'

const count1 = ref(0)

watch(() => {
  return count1.value
},
(newValue, oldValue) => {
  console.log('newValue', newValue)
  console.log('oldValue', oldValue)
})
</script>
```
* 第一引数に指定された関数はwatchEffectと同じように、関数内の全てのリアクティブな値が監視対象になる
<br>

## watch利用時の注意点
第二引数に指定された変更前・変更後の値が同じだった場合、第二引数の関数は実行されない
<br>

## 第一引数に配列を指定する場合
第一引数に配列を指定することにより、複数の値を監視対象に指定することができる
<br>

例）
```
watch([count1, count2], (newValue, oldValue) => {
  console.log('newValue', newValue)
  console.log('oldValue', oldValue)
})
```
* 指定したそれぞれの値に対して、変更前・変更後の値が配列として格納される
