# setup()関数とは
コンポーネントのロジックを定義するための特別な関数  
    ⇒ コンポーネント内でデータやメソッドを定義するために使う
<br>

※setup()関数はVue3から。それ以前のバージョンには存在しないため、コンポーネント内で定義するにはそれ用のオプションを使用する必要がある。

## Vue2の場合(setupなし)
```
<script>
export default {
    data() {
        return {
            message: "メッセージ",
        };
    },
    methods: {
        updateMessage() {
            this.message = "新しいメッセージ"
        }
    },
};
</script>
```

## Vue3の場合(setupあり)
```
<script setup>
import { ref } from "Vue";

const message = ref("メッセージ");

const updateMassage = () => {
    message = "新しいメッセージ";
};
</script>
```

<br><br>

# setupの使い方
* scriptタグ内に`<script setup>`と記述
* 通常のJavaScriptのようにデータやメソッドを定義

※タグ内には記述せず、setup()を関数として定義することも出来るが、冗長になる。

## setup()関数として定義する場合
```
<script>
import { ref } from "Vue";

export default {
    setup() {
        const count = ref(0);

        const increment = () => {
            count.value++;
        };

        return {
            count,
            increment,
        };
    },
};
</script>
```

## タグ内に書く場合
```
<script setup>
import { ref } from "Vue";

const count = ref(0);

const increment = () => {
    count.value++;
};
</script>
```