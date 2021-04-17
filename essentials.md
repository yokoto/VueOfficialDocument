# 基本的な使い方

## コンポーネントの基本

### 親コンポーネントで子コンポーネントのイベントを購読する($emit)

親コンポーネントとの通信が必要になる場合がある。

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

#### イベントを特定の値と一緒にディスッパッチしたい場合

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
