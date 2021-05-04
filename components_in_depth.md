# コンポーネントの詳細

## 0. コンポーネントの登録

### グローバル登録

* 以下は、登録後に作成された、全てのルート Vue インスタンス( `new Vue` )のテンプレート内で使用できることを意味する。

```html
<div id="app">
  <component-a></component-a>
  <component-b></component-b>
  <component-c></component-c>
</div>
```

```js
Vue.component('component-a', { /* ... */ })
Vue.component('component-b', { /* ... */ })
Vue.component('component-c', { /* ... */ })

new Vue({ el: '#app' })
```

### ローカル登録

* コンポーネントを素の JavaScript オブジェクトとして定義し、 次に `components` オプション内に使いたいコンポーネントを定義する。

```js
var ComponentA = { /* ... */ }
var ComponentB = { /* ... */ }
var ComponentC = { /* ... */ }

new Vue({
  el: '#app',
  components: {
    'component-a': ComponentA,
    'component-b': ComponentB
  }
})
```

* ローカル登録されたコンポーネントは、他のサブコンポーネント内では使用できない。
* `ComponentA` を `ComponentB` 内で使用可能にしたいときは、以下のように使う必要がある。

```js
var ComponentA = { /* ... */ }

var ComponentB = {
  components: {
    'component-a': ComponentA
  }
}
```

```js
// ComponentB.vue
import ComponentA from './ComponentA.vue'

export default {
  components: {
    ComponentA // ComponentA: ComponentA の省略記法
  },
  // ...
}
```

### モジュールシステム

#### 基底コンポーネントの自動グローバル登録

* 基底コンポーネント
  * `input` や `button` のような要素をラップした、複数のコンポーネントを横断して用いられるコンポーネント。

* Webpack を使用しているなら、基底コンポーネントのグローバル登録に `require.context` を用いることができるが、使用しない場合は以下のようになる。

```html
<BaseInput
  v-model="searchText"
  @keydown.enter="search"
/>
<BaseButton @click="search">
  <BaseIcon name="search"/>
</BaseButton>
```

```js
import BaseButton from './BaseButton.vue'
import BaseIcon from './BaseIcon.vue'
import BaseInput from './BaseInput.vue'

export default {
  components: {
    BaseButton,
    BaseIcon,
    BaseInput
  }
}
```

## 1. プロパティ

### 単方向データフロー

* 親のプロパティが更新される場合は子へと流れ落ちるが、その逆はない。
  * 子コンポーネントが親の状態を変更するような、アプリのデータフローを理解しづらくすることを防ぐ。
* 親コンポーネントが更新されるたびに、子コンポーネントの全てのプロパティは最新の値に更新される。
  * 子コンポーネント内でプロパティの値を変化させてはいけない。

* 子コンポーネント内でプロパティの値を変化させたい場合は主に2つ。

1. プロパティを初期値として受け渡し、子コンポーネントにてローカルのデータとして後で利用したいと考える場合。
→ プロパティの値を初期値として使うローカルの data プロパティを定義するのがベスト。

```js
props: ['initialCounter'],
data() {
  return {
    counter: this.initialCounter
  }
}
```

2. プロパティを変換が必要な未加工の値として受け渡す場合。
→ プロパティの値を使用した算出プロパティを別途定義するのがベスト。

```js
props: ['size'],
computed: {
  normilizedSize: function () {
    return this.size.trim().toLowerCase()
  }
}
```

### プロパティのバリデーション

* プロパティのバリデーションが失敗した場合、Vue はコンソールに警告を表示します。
* プロパティのバリデーションはコンポーネントのインスタンスが生成される前に行われるため、インスタンスのプロパティ（例えば `data` , `computed` など）を `default` 及び `validator` 関数の中で利用することはできません。

```js
app.component('my-component', {
  props: {
    propA: Number,
    propB: [String, Number],  // 複数の型の許容
    propC: {
      type: String,
      required: true
    },
    propD: {
      type: Number,
      default: 100
    },
    propE: {
      type: Object,
      // オブジェクトもしくは配列のデフォルト値は
      // 必ずファクトリ関数（それを生み出すための関数）を返す必要がある。
      default: function() {
        return { message: 'hello' }
      }
    },
    propF: {
      validator: function(value) {
        // プロパティの値は、必ずいずれかの文字列でなければならない
        return ['success', 'warning', 'danger'].indexOf(value) !== -1
      }
    },
    propG: {
      type: Function,
      // オブジェクトや配列のデフォルトとは異なり、これはファクトリ関数ではありません。
      // デフォルト値としての関数を取り扱います。
      default: function() {
        return 'Default function'
      }
    }
  }
})
```

### プロパティの形式（キャメルケース vs ケバブケース）

* ブラウザは全ての大文字を小文字として解釈する。

```html
<!-- HTML 内ではケバブケース -->
<blog-post post-title="hello">
```

```js
const app = Vue.createApp({})

app.component('blog-post', {
  // JavaScript 内ではキャメルケース
  props: ['postTitle'],
  template: '<h3>{{ postTitle }}</h3>'
})
```

## 2. プロパティでない属性

* プロパティでない属性
  * `props` や `emits` で定義されたものを除いたもの
  * `class` 、 `style` 、 `id` などの属性
  * `$attrs` プロパティでアクセスできる。

## 3. カスタムイベント

* 子コンポーネントから発行すると、親コンポーネントでリスナを追加できるようになる。

```html
<my-component @my-event="doSomething"></my-component>
```

```js
this.$emit('myEvent')
```

## 4. スロット

* 親となるコンポーネント側から、子のコンポーネントのテンプレートの一部を差し込む機能。
* 親側でスロットコンテンツが定義されていた場合は、 `<slot>` タグで囲まれたコンポーネント側のコンテンツは表示されず、親側のスロットコンテンツが表示される。

```html
<!-- myCom.vue -->
<template>
  <div class="mycom">
    <!-- name: 未来太郎 と出力される -->
    <p>name: <slot>Mirai Taro</slot></p>
  </div>
</template>
<style></style>
```

```html
<!-- About.vue -->
<template>
  <div class="home">
    <MyCom>未来太郎</MyCom>
  </div>
</template>
<script>
  import MyCom from '../components/MyCom.vue'
  export default {
    components: {
      MyCom
    }
  }
</script>
```
https://future-architect.github.io/articles/20200428/

## 5. Provide / Inject
## 6. 動的 & 非同期コンポーネント
## 7. テンプレート参照について
## 8. 特別な問題に対処する
