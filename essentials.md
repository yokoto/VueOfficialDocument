# 基本的な使い方

## はじめに

### Vue.js とは？

* ユーザーインターフェイスを構築するためのプログレッシブフレームワーク。
* モノリシックなフレームワークとは異なり、Vueは少しずつ適用していけるよう設計されている。

### 宣言的レンダリング

* Vue.js は、単純なテンプレート構文を使って宣言的にDOMにレンダリングする。
* 要素の属性の束縛 = バインディング。
* ディレクティブ
  * Vue によって提供された特別な属性
  * `v-` 接頭辞がついている
  * レンダリングされたDOMに特定のリアクティブな振る舞いを与える

```html
<div id="bind-attribute">
  <!-- この要素の title 属性を、現在アクティブなインスタンスにおける message プロパティの最新状態に維持する -->
  <span v-bind:title="message">
    Hover your mouse over me for a few seconds to see my dynamically bound title!
  </span>
</div>
```

```js
const AttributeBinding = {
  data() {
    return {
      message: 'You loaded this page on ' + new Date().toLocaleString()
    }
  }
}

Vue.createApp(AttributeBinding).mount('#bind-attribute')
```

### ユーザー入力の制御

* `v-on` ディレクティブを使ってイベントリスナをアタッチし、インスタンスのメソッドを呼び出すことができる。

```html
<div id="event-handling">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">Reverse Message</button>
</div>
```

```js
const EventHandling = {
  data() {
    return {
      message: 'Hello Vue.js!'
    }
  },
  methods: {
    reverseMessage() {
      this.message = this.message
        .split('')
        .reverse()
        .join('')
    }
  }
}

Vue.createApp(EventHandling).mount('#event-handling')
```

### コンポーネントによる構成

- ほぼすべてのタイプのアプリケーションインターフェイスは、コンポーネントツリーとして抽象化できる。

```html
<div id="todo-list-app">
  <ol>
   <!--
    todo-item コンポーネントのインスタンスを生成する

    各 todo-itemにその内容を表す todo オブジェクトを指定することで、
    内容が動的に変化します。後述する "key" も
    各コンポーネントに指定する必要があります。
   -->
   <todo-item
    v-for="item in groceryList"
    v-bind:todo="item"
    v-bind:key="item.id"
   ></todo-item>
  </ol>
</div>
```

```js
const TodoList = {
  data() {
    return {
      groceryList: [
        { id: 0, text: 'Vegetables' },
        { id: 1, text: 'Cheese' },
        { id: 2, text: 'Whatever else humans are supposed to eat' }
      ]
    }
  }
}

// アプリケーションインスタンスの生成
const app = Vue.createApp(TodoList)

// 新しいコンポーネントの定義
app.component('todo-item', {
  props: ['todo'],
  template: `<li>{{ todo.text }}</li>`
})

// アプリケーションのマウント
app.mount('#todo-list-app')
```

## アプリケーションインスタンス

### インスタンスの作成

* `createApp` 関数
  * 新しいアプリケーションインスタンスを作成する。
  * すべての Vue アプリケーションはここから始まる。
* `mount` メソッド
  * アプリケーションインスタンスをコンテナ( `<div id="app"></div>` など)にマウントできる。

```js
Vue.createApp(/* options */).mount('#app')
```

* Vue の設計は MVVM パターンに影響を受けているため、インスタンスを参照するのに変数 `vm` (ViewModelの短縮形) を使用する。
* Vue アプリケーションは、 `createApp` で作られたひとつのルートインスタンスと、入れ子になった再利用可能なコンポーネントのツリーから成る。

```
Root Instance
└─ TodoList
   ├─ TodoItem
   │  ├─ DeleteTodoButton
   │  └─ EditTodoButton
   └─ TodoListFooter
      ├─ ClearTodosButton
      └─ TodoListStatistics
```

### データとメソッド

* `data` 
  * `data` で見つけられる全てのプロパティは、Vue のリアクティブシステムに追加される。
  * `data` のプロパティが変更されると、ビューは反応(react)し、再レンダリングされる。
  * `data` のプロパティは、インスタンス作成時に存在した場合にのみリアクティブである。 = 何らかの初期値が必要。

### インスタンスライフサイクルフック

* それぞれのインスタンスは、作成時に連の初期化の手順を踏む。
* インスタンスは、特定の段階にコードを追加する機会をユーザに与えるために、ライフサイクルフックと呼ばれる関数を実行する。
* 全てのライフサイクルフックは、呼び出し元である現在アクティブなインスタンスを指す `this` コンテキストとともに呼ばれる。
  * アロー関数は `this` を持たないため、ライフサイクルフックの定義に使用しないこと。

* `created` フック
  * インスタンスの作成後にコードを実行するために使用できる。

```js
Vue.createApp({
  data() {
    return {
      a: 1
    }
  },
  created() {
    // `this` は vm インスタンスを指します
    console.log('a is: ' + this.a) // => "a is: 1"
  }
})
```

### ライフサイクルダイアグラム

<img width="501" alt="スクリーンショット 2021-05-03 18 52 37" src="https://user-images.githubusercontent.com/6859224/116863120-c5b83400-ac40-11eb-9a7a-3114ba13a04f.png">

## テンプレート構文

### 展開

* 二重中括弧の mustaches
  * データを HTML ではなく、プレーンなテキストとして扱う。

```html
<p>Using mustaches: {{ rawHtml }}</p>
<p>Using v-html directive: <span v-html="rawHtml"></span></p>
```

```js
const RenderHtmlApp = {
  data() {
    return {
      rawHtml: '<span style="color: red">This should be red.</span>'
    }
  }
}

Vue.createApp(RenderHtmlApp).mount('#example1')
```

### ディレクティブ

#### 引数

* ディレクティブ名の後にコロンで表記することで、引数を取ることができる。

```html
<!-- url の値を、要素の href 属性へバインドする -->
<a v-bind:href="url">...</a>
```

### 省略記法

#### v-bind

```html
<a v-bind:href="url">...</a>

<a :href="url">...</a>

<!-- 動的引数の省略記法 -->
<a :[key]="url">...</a>
```

#### v-on

```html
<a v-on:click="doSomething">...</a>

<a @click="doSomething">...</a>

<!-- 動的引数の省略記法 -->
<a @[event]="doSomething">...</a>
```

## データプロパティとメソッド

### データプロパティ

* `data` オプション
  * `data` は、新しいコンポーネントのインスタンスを作成する際に呼び出される関数。
  * Vue は `data` が返したオブジェクトをリアクティブシステムでラップして、コンポーネントのインスタンスに `$data` として格納する。
  * 便宜上、 `data` が返したオブジェクトのトップレベルのプロパティは、コンポーネントのインスタンスを介して直接公開される。

### メソッド

* `methods` オプション
  * コンポーネントのインスタンスにメソッドを追加する。
  * `methods` は、必要なメソッドを含むオブジェクトでなければならない。
  * コンポーネントのテンプレート内からアクセスでき、よくイベントリスナとして使われる。

```html
<button @click="increment">Up vote</button>
```

```js
const app = Vue.createApp({
  data() {
    return { count: 4 }
  },
  methods: {
    increment() {
      // `this` はコンポーネントインスタンスを参照
      this.count++
    }
  }
})

const vm = app.mount('#app')

console.log(vm.count) // => 4

vm.increment()

console.log(vm.count) // => 5
```

## 算出プロパティとウォッチャ

### 算出プロパティ vs メソッド

* 算出プロパティはリアクティブな依存関係に基づいてキャッシュされる。
  * リアクティブな依存関係の一部が変更された場合にのみ再評価される。
* メソッドは、再レンダリングが起こるたびに常に関数を実行する。

### 算出 Setter 関数

* 以下の状態では、 `vm.fullName = 'John Doe` を実行すると setter 関数が呼び出され、その結果 `vm.firstName` と `vm.lastName` が更新される。

```js
// ...
computed: {
  fullName: {
    // getter 関数
    get() {
      return this.firstName + ' ' + this.lastName
    },
    // setter 関数
    set(newValue) {
      const names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
// ...
```

### ウォッチャ

* `watch` オブション
  * データを変更するのに応じて非同期処理や重い処理を実行したい場合に最も便利。

```html
<div id="watch-example">
 <p>
   Ask a yes/no question:
   <input v-model="question" />
 </p>
</div>
```

```js
<!-- ajax ライブラリや汎用ユーティリティメソッドのコレクションなどの -->
<!-- 豊富なエコシステムが既に存在するため、それらを再発明しないことで -->
<!-- Vue のコアは小規模なまま保たれています。これは、使い慣れたものを -->
<!-- 自由に使うことができる、ということでもあります。 -->
<script src="https://cdn.jsdelivr.net/npm/axios@0.12.0/dist/axios.min.js"></script>
<script>
  const watchExampleVM = Vue.createApp({
    data() {
      return {
        question: '',
        answer: 'Questions usually contain a question mark. ;-)'
      }
    },
    watch: {
      // question が変わるたびに、この関数が実行される
      question(newQuestion, oldQuestion) {
        if (newQuestion.indexOf('?') > -1) {
          this.getAnswer()
        }
      }
    },
    methods: {
      getAnswer() {
        this.answer = 'Thinking...'
        axios
          .get('https://yesno.wtf/api')
          .then(response => {
            this.answer = response.data.answer
          })
          .catch(error => {
            this.answer = 'Error! Could not reach the API.' + error
          })
      }
    }
  }).mount('#watch-example')
</script>
```

### 算出プロパティ(computed) vs 監視プロパティ(watch)

* 他のデータに基づいて変更しなければならないデータがある場合、`watch` を使いすぎてしまいがちだが、たいていの場合は算出プロパティを使うのがベター。

```html
<div id="demo">{{ fullName }}</div>
```

```js
// 監視プロパティを使った場合
const vm = Vue.createApp({
  data() {
    return {
      firstName: 'Foo',
      lastName: 'Bar',
      fullName: 'Foo Bar'
    }
  },
  watch: {
    firstName(val) {
      this.fullName = val + ' ' + this.lastName
    },
    lastName(val) {
      this.fullName = this.firstName + ' ' + val
    }
  }
}).mount('#demo')

// 算出プロパティを使った場合
const vm = Vue.createApp({
  data() {
    return {
      firstName: 'Foo',
      lastName: 'Bar'
    }
  },
  computed: {
    fullName() {
      return this.firstName + ' ' + this.lastName
    }
  }
}).mount('#demo')
```

## クラスとスタイルのバインディング

* `:class`
  * クラスを動的に切り替えることができる
* `:style`
  * 要素のスタイルを動的に切り替えることができる

## 条件付きレンダリング

### `v-if` と `v-show`

* `v-show`
  * 要素は常に描画されて DOM に維持される。
  * 要素の `display` CSSプロパティを切り替える。
* とても頻繁に何かを切り替える必要があれば `v-show` を選び、条件が実行時に変更することがほとんどない場合は、 `v-if` を選ぶ。

## リストレンダリング

### オブジェクトの `v-for`

```html
<li v-for="(value, name, index) in myObject">
 {{ index }}. {{ name }}: {{ value }}
</li>
<!--
以下のように描画される。
・ 0. title: How to do lists in Vue
・ 1. author: Jane Doe
・ 2. publishedAt: 2016-04-10
-->
```

```js
Vue.createApp({
  data() {
    myObject: {
      title: 'How to do lists in Vue',
      author: 'Jane Doe',
      publishedAt: '2016-04-10'
    }
  }
}).mount('#v-for-object')
```

### コンポーネントと `v-for`

* 繰り返されたデータをコンポーネントに渡すには、プロパティ(以下の例では `title`)を渡す必要がある。

```html
<div id="todo-list-example">
 <form v-on:submit.prevent="addNewTodo">
  <label for="new-todo">Add a todo</label>
  <input
    v-model="newTodoText"
    id="new-todo"
    placeholder="E.g. Feed the cat"
  />
  <button>Add</button>
 </form>
 <ul>
   <todo-item
     v-for="(todo, index) in todos"
     :key="todo.id"
     :title="todo.title"
     @remove="todos.splice(index, 1)"
   ></todo-item>
 </ul>
</div>
```

```js
const app = Vue.createApp({
  data() {
    return {
      newTodoText: '',
      todos: [
        {
          id: 1,
          title: 'Do the dishes'
        },
        {
          id: 2,
          title: 'Take out the trash'
        },
        {
          id: 3,
          title: 'Mow the lawn'
        }
      ],
      nextTodoId: 4
    }
  },
  methods: {
    addNewTodo() {
      this.todos.push({
        id: this.nextTodoId++,
        title: this.newTodoText
      })
      this.newTodoText = ''
    }
  }
})

app.component('todo-item', {
  template: `
    <li>
      {{ title }}
      <button @click="$emit('remove')">Remove</button>
    </li>
  `,
  props: ['title'],
  emits: ['remove']
})

app.mount('#todo-list-example')
```

## イベントハンドリング

## フォーム入力バインディング

### 基本的な使い方(v-model)

* `v-model` ディレクティブ
  * form 要素( `input` , `textarea` , `select` )に双方向(two-way)データバインディングを作成する
  * 内部的に input 要素に応じて異なるプロパティを使用し、異なるイベントを `emit` する  

### 値のバインディング(v-bind)

* `v-bind` ディレクティブ
  * input 値に文字列(チェックボックスなら boolean)以外の値を束縛することができるようになる

## コンポーネントの基本

### 親コンポーネントで子コンポーネントのイベントを購読する($emit)

* 親コンポーネントでは `v-on` を使って子コンポーネントで起きた任意のイベントを購読することができ、  
子コンポーネントではビルトインの `$emit` メソッドにイベントの名前を渡して呼び出すことで、  
イベントを `emit` することができる。  
※ `emit` する、とは、 「イベントをディスパッチした(発生させた)時に、イベントをリッスン（登録)しているコールバック関数(イベントリスナー)を呼び出すこと」 参照: https://jsprimer.net/use-case/todoapp/event-model/#event-emitter

例: ブログ記事の文字の文字を拡大する機能の実装。


```html
# blog_posts/index.html
<div id="blog-posts-events-demo" class="demo">
  <div :style="{ fontSize: postFontSize + 'em' }">
    <blog-post
       v-for="post in posts"
       :key="post.id"
       :title="post.title"
       @enlarge-text="postFontSize += 0.1" // 子コンポーネントのインスタンスでのイベントの購読
    ></blog-post>
  </div>
</div>
```

```js
// BlogPost.vue
const app = Vue.createApp({
  data() {
    return {
      posts: [
        { id: 1, title: 'My journey with Vue'},
        { id: 2, title: 'Blogging with Vue'},
        { id: 3, title: 'Why Vue is so fun'}
      ],
      postFontSize: 1
    }
  }
})

app.component('blog-post', {
  props: ['title'],
  // $emit メソッドによってイベントは「子から親」 に渡され、
  // 親コンポーネントの `v-on:enlarge-text="postFontSize += 1" によってリッスンされたイベントがディスパッチされる
  template: `
    <div class="blog-post">
      <h4>{{ title }}</h4>
      <button @click="$emit('enlargeText')"> 
        Enlarge text
      </button>
    </div>
  `
})

app.mount('#blog-posts-events-demo')
```

#### イベントリスナーに値を渡したい場合

`$emit` の第二引数と `$event` を使う。

```html
<blog-post ... @enlarge-text="postFontSize += $event"></blog-post>
```

```js
<button @click="$emit('enlargeText', 0.1)">
  Enlarge text
</button>
```

#### イベントハンドラがメソッドの場合

値は子コンポーネントで定義されたそのメソッドの、第一引数として渡される。

```html
<blog-post ... @enlarge-text="onEnlargeText"></blog-post>
```

```js
methods: {
  onEnlargeText(enlargeAmount) {
    this.postFontSize += enlargeAmount
  }
}
```

#### コンポーネントでv-modelを使う

カスタムイベントは `v-model` で動作するカスタム入力を作成することもできる。

```html
<!-- 以下の例は同じことをしている -->
<input v-model="searchText">

<input
  v-bind:value="searchText"
  v-on:input="searchText = $event.target.value"
>
```


