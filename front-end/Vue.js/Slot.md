# Slotの役割
親コンポーネントから子コンポーネントにデータを渡す
<br>

## propsとSlotの違い
* propsが渡すデータ...数字や文字列
* slotsが渡すデータ...html構文

<br><br>

# 渡し方
App.vue(親側)
```
<script setup>
import BassCard from './components/BassCard.vue';
</script>

<template>
  <h1>Slots</h1>
  <BassCard>
    <h2>Hello</h2>
  </BassCard>
</template>
```
* 子コンポーネントのタグ内に渡したいhtml構文を記述
<br>

BassCard.vue(子側)
```
<template>
  <div>
    <slot></slot>
  </div>
</template>
```
* 表示したい部分にslotタグを記述
<br>

※親コンポーネント内で定義されたデータにはアクセスできるが、子コンポーネントで定義されたデータにはアクセスできない

<br><br>

# デフォルトの表示内容
App.vue
```
<script setup>
import BassCard from './components/BassCard.vue';
</script>

<template>
  <BassCard />
</template>
```
<br>

BassCard.vue(子側)
```
<template>
  <div>
    <slot>
      <p>No Content</p>
    </slot>
  </div>
</template>
```
親側のコンポーネントタグ内に何も指定が無かった場合、子コンポーネントのslotタグ内に記述されたhtml構文(フォールバックコンテンツ)がデフォルト表示される

<br><br>

# slotタグの複数指定
App.vue(親側)
```
<script setup>
import BassCard from './components/BassCard.vue';
</script>

<template>
  <h1>Slots</h1>
  <BassCard>
    <template #header>
      <h2>Vue.js Course</h2>
    </template>
    <template #main>
      <p>This is a Vue,js Course. You can learn Vue.js.</p>
    </template>
    <template #footer>
      <p>Instructor: Yoshipi</p>
    </template>
  </BassCard>
</template>
```
* 子コンポーネントタグ内にtemplateタグを複数用意
* 各templateタグに`v-slot:識別子(省略記法　#識別子)`を記述
<br>

BassCard.vue(子側)
```
<template>
  <div>
    <header>
      <slot name="header"/>
    </header>
    <main>
      <slot name="main"/>
    </main>
    <footer>
      <slot name="footer"/>
    </footer>
  </div>
</template>
```
* slotタグを複数用意
* slotタグ内に`name="識別子"`の形で、親側で指定した識別子を記述

<br><br>

# slotを使って子から親コンポーネントにデータを渡す
## 名前付きslotタグを使う場合(slotタグが複数ある場合)
BassCard.vue(子側)
```
<script setup>
import { ref } from 'vue';

const pageCount = ref(1)
</script>

<template>
  <div>
    <button @click="pageCount=1">1</button>
    <button @click="pageCount=2">2</button>
    <button @click="pageCount=3">3</button>
    <header>
      <slot name="header" :page-count="pageCount"/>
    </header>
  </div>
</template>
```
* slotタグ内に`:名前="渡すデータ"`の形でslotPropsを記述
<br>

App.vue
```
<script setup>
import BassCard from './components/BassCard.vue';
</script>

<template>
  <h1>Slots</h1>
  <BassCard>
    <template #header="{ pageCount }">
      <h2 v-if="pageCount === 1">Vue.js Course</h2>
      <h2 v-if="pageCount === 2">HTML Course</h2>
      <h2 v-if="pageCount === 3">CSS Course</h2>
    </template>
  </BassCard>
</template>
```
* templateタグ内に`#識別子="{ 受け取るデータ }"`を記述
    * 受け取ったtemplate内でのみ、データを扱うことができる
<br>

## 名前付きslotタグを使わない場合(slotタグが単体の場合)
BassCard.vue(子側)
```
<script setup>
import { ref } from 'vue';

const pageCount = ref(1)
</script>

<template>
  <div>
    <button @click="pageCount=1">1</button>
    <button @click="pageCount=2">2</button>
    <button @click="pageCount=3">3</button>
    <slot :page-count="pageCount"/>
  </div>
</template>
```
<br>

App.vue(親側)
```
<script setup>
import BassCard from './components/BassCard.vue';
</script>

<template>
  <h1>Slots</h1>
  <BassCard v-slot="{ pageCount }">
    <h2 v-if="pageCount === 1">Vue.js Course</h2>
    <h2 v-if="pageCount === 2">HTML Course</h2>
    <h2 v-if="pageCount === 3">CSS Course</h2>
  </BassCard>
</template>
```
* コンポーネントタグ内に`v-slot="{ 受け取るデータ }"`を記述