# Vuex とは何か？

::: tip 注意
これは、Vue 3 で動作する Vuex 4 のためのドキュメントです。Vue 2 で動作する Vuex 3 のドキュメントをお探しの方は、[こちらをご覧ください](https://vuex.vuejs.org/ja/)。
:::

Vuex は Vue.js アプリケーションのための **状態管理パターン + ライブラリ**です。
これは予測可能な方法によってのみ状態の変異を行うというルールを保証し、アプリケーション内の全てのコンポーネントのための集中型のストアとして機能します。

## "状態管理パターン"とはなんですか？

単純な Vue で作られたカウンターアプリをみてみましょう:

```js
const Counter = {
  // state
  data () {
    return {
      count: 0
    }
  },
  // view
  template: `
    <div>{{ count }}</div>
  `,
  // actions
  methods: {
    increment () {
      this.count++
    }
  }
}

createApp(Counter).mount('#app')
```

これはいくつかの要素をアプリ自身に含んでいます:

- **状態**、これは私達のアプリを動かす信頼できる情報源(the source of truth)です。
- **ビュー**、これは**状態**のただの宣言的なマッピングです。
- **アクション**、これは**ビュー**からのユーザー入力に反応して、状態の変更を可能にする方法です。

これらは"単方向データフロー"のコンセプトの極めてシンプルな責務です:

<p style="text-align: center; margin: 2em">
  <img style="width:100%; max-width:450px;" src="/flow.png">
</p>

しかし、単純さは、**共通の状態を共有する複数のコンポーネントを持ったときに**、すぐに破綻します:

- 複数のビューが同じ状態に依存することがあります。
- 異なるビューからのアクションで、同じ状態を変更する必要があります。

一つ目は、プロパティ (props) として深く入れ子になったコンポーネントに渡すのは面倒で、兄弟コンポーネントでは単純に機能しません。二つ目は、親子のインスタンスを直接参照したり、イベントを介して複数の状態のコピーを変更、同期することを試みるソリューションに頼っていることがよくあります。これらのパターンは、いずれも脆く、すぐにメンテナンスが困難なコードに繋がります。

では、コンポーネントから共有している状態を抽出し、それをグローバルシングルトンで管理するのはどうでしょうか？ これにより、コンポーネントツリーは大きな "ビュー" となり、どのコンポーネントもツリー内のどこにあっても状態にアクセスしたり、アクションをトリガーできます!

さらに、状態管理に関わる概念を定義、分離し、特定のルールを敷くことで、コードの構造と保守性を向上させることができます。

これが Vuex の背景にある基本的なアイディアであり、[Flux](https://facebook.github.io/flux/docs/overview)、 [Redux](http://redux.js.org/) そして [The Elm Architecture](https://guide.elm-lang.org/architecture/)から影響を受けています。
他のパターンと異なるのは、Vuex は効率的な更新のために、Vue.js の粒度の細かいリアクティビティシステムを利用するよう特別に調整して実装されたライブラリだということです。

あなたがもし対話型の方法でVuexを学びたいのであれば、[Scrimba](https://scrimba.com/g/gvuex)のVuexコースをぜひ試してみてください。

![vuex](/vuex.png)

## いつ、Vuexを使うべきでしょうか？

Vuex は、共有状態の管理に役立ちますが、さらに概念やボイラープレートのコストがかかります。これは、短期的生産性と長期的生産性のトレードオフです。

もし、あなたが大規模な SPA を構築することなく、Vuex を導入した場合、冗長で気が遠くなるように感じるかもしれません。そう感じることは全く普通です。あなたのアプリがシンプルであれば、Vuex なしで問題ないでしょう。単純な [ストアパターン](https://v3.ja.vuejs.org/guide/state-management.html#simple-state-management-from-scratch) が必要なだけかもしれません。しかし、今あなたが中規模から大規模の SPA を構築しているなら、Vue コンポーネントの外の状態をもっとうまく扱えないか考えなくてはならない状況にあるかもしれません。その場合 Vuex は次のステップとして最適でしょう。これは Redux の作者、Dan Abramov からの良い引用です:

> Flux ライブラリは眼鏡のようなものです: あなたが必要な時にいつでも分かるのです。