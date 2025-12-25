# テキスト
```
<script setup>
import { ref } from 'vue'

const userInput = ref('')
const message = ref('')
</script>

<template>
  <h1>v-model</h1>
  <h2>Text</h2>
  <input v-model="userInput" type="text" />
  <p>{{ userInput }}</p>

  <h2>Textarea</h2>
  <textarea v-model="message"></textarea>
  <p style="white-space: pre">{{ message }}</p>
</template>
```
* `white-space: pre`...テキストエリアの改行を反映できる
<br>

## 修飾子
`<input v-model.lazy.trim.number="userInput" type="text" />`
<br>

### lazy
* 入力された内容が、即時反映ではなくフォーカスが外れた時に反映される
* typeがtextのinputとtextareaに利用できる
<br>

### trim
* 文字列の最初と最後の空白と空行を取り除く(文字間の物は取り除かない)
* typeがtextのinputとtextareaに利用できる
<br>

### number
* 数字が入力された時、自動的に文字列型から数値型に変換する
* typeがtextのinputとtextarea、セレクトボックスに利用できる

<br><br>

# チェックボックス
```
<script setup>
import { ref } from 'vue'

const checked = ref('not checked')
const fruits = ref([])
</script>

<template>
  <h2>Checkbox</h2>
  <input
    id="checkbox"
    v-model="checked"
    type="checkbox"
    true-value="checked"
    false-value="not checked"
  />
  <label for="checkbox">{{ checked }}</label>

  <p>Fruits</p>
  <input id="Apple" v-model="fruits" type="checkbox" value="Apple">
  <label for="Apple">Apple</label>
  <input id="Banana" v-model="fruits" type="checkbox" value="Banana">
  <label for="Banana">Banana</label>
  <input id="Grape" v-model="fruits" type="checkbox" value="Grape">
  <label for="Grape">Grape</label>
  <p>{{ fruits }}</p>
</template>
```
* `true-value`...チェックされている時の値を指定できる
* `false-value`...チェックされていない時の値を指定できる
* 初期値を配列にすることで、複数選択に対応できる
    * 複数選択の場合、配列に格納される値を`value`で指定する

<br><br>

# ラジオボタン
```
<script setup>
import { ref } from 'vue'

const gender = ref('male')
</script>

<template>
  <h2>Radio</h2>
  <input id="male" v-model="gender" type="radio" value="male">
  <label for="male">male</label>
  <input id="female" v-model="gender" type="radio" value="female">
  <label for="female">female</label>
  <p>{{ gender }}</p>
</template>
```
* 選択されたいずれかの値をとるため、`value`が必要

<br><br>

# セレクトボックス
```
<script setup>
import { ref } from 'vue'

const selected = ref([])
</script>

<template>
  <h2>Select</h2>
  <select v-model="selected" multiple>
    <option value="" disabled>Select one</option>
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
  <p>{{ selected }}</p>
</template>
```
* `value`を指定しなくても、選択された内容が格納される
* `<option value="" disabled>`で初期表示を設定できる
    * `disabled`により、選択することは出来なくする
* 初期値を配列にすることで、複数選択に対応できる
    * `multiple`でセレクトボックスを複数選択可能にする
    * shiftやctrl同時押しで複数選択できる

<br><br>

# コンポーネントへのv-modelの利用
親側
```
<script setup>
import { ref } from 'vue';
import CustomInput from './components/CustomInput.vue';

const userInput = ref('hello')
const titleInput = ref('title')
</script>

<template>
  <h1>v-model</h1>
  <CustomInput v-model="userInput" v-model:title-name="titleInput"/>
  <p>userInput: {{ userInput }}</p>
  <p>titleInput: {{ titleInput }}</p>
</template>
```
<br>

子側
```
<script setup>
const model = defineModel({
  type: String,
  default: 'default',
})
const titleName = defineModel('title-name', {
  type: String,
  required: true,
})
</script>

<template>
  <h2>CustomInput</h2>
  <input v-model="model" type="text" />
  <p>mode: {{ model }}</p>
  <input v-model="titleName" type="text" />
  <p>model: {{ titleName }}</p>
</template>
```
* 親側のコンポーネントタグにv-modelを定義
* 子側にdefineModelを定義
  * defineModelの返り値(model)を親コンポーネントに渡すことができる
* defineModelの引数にオブジェクトを指定して、バリデーションを設定できる
* `v-model:識別子=""`をつけることで、複数のデータを渡すことができる
  * 最初の１つだけ、識別子不要
  * v-modelの数だけdefineModelが必要
    * 第一引数に識別子を指定。バリデーションが必要な場合、第二引数に指定
<br>

## 修飾子の利用
親側
`<CustomInput v-model.uppercase="userInput" v-model:title-name="titleInput"/>`
<br>

子側
```
const [model, modifiers] = defineModel({
  type: String,
  default: 'default',
  set(value) {
    if (modifiers.uppercase) {
      return value.toUpperCase()
    }
    return value
  },
})
```
* defineModelの返り値を配列にする
* 配列の一つ目の要素にdefineModelの返り値を指定
* 配列の二つ目の要素に識別子の格納先を指定
* set()内で識別子による処理を定義