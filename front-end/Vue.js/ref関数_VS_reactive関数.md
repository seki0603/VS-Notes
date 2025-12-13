```
<script setup>
import { reactive, ref } from 'vue'

let price = ref(9.99)

const info = ref({
  students: 1000,
  rating: 4,
})
const instructor = reactive({
  name: 'Yoshipi',
  age: 25,
})

function increment() {
  price.value += 1
  info.value.students += 1
  instructor.age += 1
}
</script>

<template>
  <h2>Price: ${{ price - 1 }}</h2>
  <h2>students: {{ info.students }}</h2>
  <h2>instructor: {{ instructor.age }}</h2>
  <button @click="increment">button</button>
</template>
```

# ref関数(公式推奨)
データをリアクティブにする(変更をブラウザに即時反映する)  
データがオブジェクトに変換される
* script内でインポート
* リアクティブ化するデータをref()で定義
* scriptで扱う場合、`データ名.value(.キー)`でアクセス
* HTMLで扱う場合は`{{ データ名(.キー) }}`...valueは不要
<br>
※ref関数でオブジェクトを定義した場合、内部的にreactive関数が使われ、データがvalueに格納される。

<br><br>

# reactive関数
オブジェクトをリアクティブにする
* script内でインポート
* リアクティブ化するデータをreactive({})で定義
* script内、HTMLともにvalue不要
<br>
※オブジェクト内にオブジェクトがあった場合、内部のオブジェクトもリアクティブ化される

# reactive関数の中でref関数を利用する
```
<script setup>
import { reactive, ref } from 'vue'

const instructor = reactive({
  name: 'Yoshipi',
  age: 25,
  email: ref('yoshipi@example.com')
})

function increment() {
  price.value += 1
  info.value.students += 1
  instructor.age += 1
  instructor.email = 'info@example.com'
}

</script>

<template>
  <h2>instructor age: {{ instructor.age }}</h2>
  <h2>instructor email: {{ instructor.email }}</h2>
  <button @click="increment">button</button>
</template>
```
script内・HTMLともにアクセスする際にvalueは不要
<br>

## reactive関数の中でref関数を配列として利用する場合
```
const items = reactive([ref(1), ref(2), ref(3)])
console.log(items[0].value)
```

script内でアクセスする際、valueが必要。