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

* `data` プロパティ
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

<img width="599" alt="スクリーンショット 2021-05-03 18 51 01" src="https://user-images.githubusercontent.com/6859224/116862977-8be72d80-ac40-11eb-808e-08c8719115b0.png">



## テンプレート構文

## 算出プロパティとウォッチャ

## クラスとスタイルのバインディング

## 条件付きレンダリング

## リストレンダリング

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


