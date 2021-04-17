# 基本的な使い方

## フォーム入力バインディング

### 基本的な使い方(v-model)

* form 要素( `input` , `textarea` , `select` )に双方向(two-way)データバインディングを作成するには、  
`v-model` ディレクティブを使用することができる。
* `v-model` ディレクティブは、内部的に input 要素に応じて異なるプロパティを使用し、異なるイベントを `emit` する。  
  * テキストと複数行テキストは、`value` プロパティと `input` イベントを使用する。
  * チェックボックスとラジオボタンは、 `checked` プロパティと `change` イベントを使用する。
  * 選択フィールドは、 `value` プロパティと `change` イベントを使用する。

#### テキスト

```html
<input v-model="message" placeholder="edit me">
<p>Message is: {{ message }}</p>
```

#### 複数行テキスト

```html
<span>Multiline message is:</span>
<p style="white-space: pre-line;">{{ message }}</p>
<br>
<!-- textarea への挿入 (<textarea>{{text}}</textarea>) は動かない。 -->
<textarea v-model="message" placeholder="add multiple lines"></textarea>
```

#### チェックボックス

```html
<!-- 単体のチェックボックスは boolean 値 -->
<input type="checkbox" id="checkbox" v-model="checked">
<label for="checkbox">{{ checked }}</label>
```

```html
<!-- 複数のチェックボックスは同じ配列に束縛する -->
<input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
<label for="jack">Jack</label>
<input type="checkbox" id="john" value="John" v-model="checkedNames">
<label for="john">John</label>
<input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
<label for="mike">Mike</label>
<br>
<span>Checked names: {{ checkedNames }}</span>
```

```js
new Vue({
  el: '...',
  data: {
    checkedNames: []
  }
})
```

#### ラジオ

```html
<input type="radio" id="one" value="One" v-model="picked">
<label for="one">One</label>
<br>
<input type="radio" id="two" value="Two" v-model="picked">
<label for="two">Two</label>
<br>
<span>Picked: {{ picked }}</span>
```

#### 選択

```html
<!-- 単体の場合 -->
<select v-model="selected">
  <option disabled value="">Please select one</option>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>
<span>Selected: {{ selected }}</span>
```

```js
new Vue({
  el: '...',
  data: {
    selected: ''
  }
})
```

```html
<!-- 複数の場合(配列) -->
<select v-model="selected" multiple>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>
<br>
<span>Selected: {{ selected }}</span>
```

```html
<!-- 動的オプション -->
<select v-model="selected">
  <option v-for="option in options" v-bind:value="option.value">
    {{ option.text }}
  </option>
</select>
<span>Selected: {{ selected }}</span>
```

```js
new Vue({
  el: '...',
  data: {
    selected: 'A',
    options: [
      { text: 'One', value: 'A' },
      { text: 'Two', value: 'B' },
      { text: 'Three', value: 'C' }
    ]
  }
})
```

### 値のバインディング(v-bind)

* `v-bind` を使用することで input 値に文字列(チェックボックスなら boolean)以外の値を束縛することができるようになる。

```html
<!-- 通常の例 -->

<!-- チェックされたとき、`picked` は文字列"a"になります -->
<input type="radio" v-model="picked" value="a">

<!-- `toggle` は true かまたは false のどちらかです -->
<input type="checkbox" v-model="toggle">

<!-- 最初のオプションが選択されたとき、`selected` は文字列"abc"です -->
<select v-model="selected">
  <option value="abc">ABC</option>
</select>
```

```html
<!-- input値に文字列以外の値を束縛する例 -->

<input type="radio" v-model="pick" v-bind:value="a">
<!--
// チェックしたとき:
vm.pick === vm.a
-->

<input
  type="checkbox"
  v-model="toggle"
  true-value="yes"
  false-value="no"
>
<!--
// チェックされたとき:
vm.toggle === 'yes'
// チェックが外されたとき:
vm.toggle === 'no'
-->

<select v-model="selected">
<!-- インラインオブジェクトリテラル -->
  <option v-bind:value="{ number: 123 }">123</option>
</select>
<!--
// 選択したとき:
typeof vm.selected // => 'object'
vm.selected.number // => 123
-->
```

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
