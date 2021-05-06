# Composition API

## はじめに

### なぜ Composition API なのか？

* 大規模なアプリケーションや、コンポーネントがより大きくなった場合、同じ論理的な関心事に関連するコードを並べることができる。

### Composition API の基本

#### `setup` コンポーネントオプション

* `setup`
  * `props` が解決されると実行され、Composition API のエントリポイントとして機能する。
  * `setup` が実行されたときはまだコンポーネントのインスタンスが作られていないため、`setup` の中では `this` を使用できない。
    * `props` を除いて、コンポーネント内で宣言されているあらゆるプロパティ（ローカルの `state` や `computed` プロパティ、 `methods`）にアクセスできない。
  * `props` と `context` を受け付ける関数であるべき。
  * `setup` から返される全てのものは、コンポーネントの残りの要素（ `computed` プロパティ、 `methods` 、ライフサイクルフックなど）およびコンポーネントの `template` に公開される。

```js
// src/components/UserRepositories.vue `setup` 関数
import { fetchUserRepositories } from '@/api/repositories'

// コンポーネント内部
setup (props) {
  // リポジトリのリスト
  let repositories = []
  // リポジトリのリストを更新する関数
  const getUserRepositories = async () => {
    repositories = await fetchUserRepositories(props.user)
  }

  // リストと関数を返して、他のコンポーネントのオプションから利用できるようにする
  return {
    repositories,
    getUserRepositories // 返される関数は methods と同様の振る舞いをします
  }
}
```

#### ref によるリアクティブな変数

#### ライフサイクルフックを setup の中に登録する

#### watch で変化に反応する

#### スタンドアロンな computed プロパティ

### セットアップ

### ライフサイクル

### Provide / Inject

### テンプレート参照

