---
title: Stencilのスタイルガイド
description: Stencilのスタイルガイド
url: /docs/style-guide
contributors:
  - jthoms1
  - natemoo-re
  - larionov
  - joestrouth1
  - rwaskiewicz
---


# Stencil Style Guide

This is a component style guide created and enforced internally by the core team of Stencil, for the purpose of standardizing Stencil components. This should only be used as a reference for other teams in creating their own style guides. Feel free to modify to your team's own preference.

> In order to enforce this (or your team's) style guide, we recommend leveraging a static analysis tool like [ESLint](https://eslint.org/). [@stencil/eslint-plugin](https://www.npmjs.com/package/@stencil/eslint-plugin) provides rules specifically for writing Stencil components.

> This guide once recommended TSLint as a static analysis tool. TSLint has been deprecated by its maintaining organization in favor of ESLint and is no longer recommended by the Stencil team.

## ファイル構造

- ファイルごとに1つのコンポーネント。
- ディレクトリごとに1つのコンポーネント。 類似のコンポーネントを同じディレクトリにグループ化することは理にかなっているかもしれませんが、各コンポーネントに独自のディレクトリがあると、コンポーネントを文書化する方が簡単であることがわかりました。
- コンポーネントの実装（.tsx）とスタイルは同じディレクトリに存在する必要があります。

Ionic Coreの例：

```bash
├── my-card
│   ├── my-card.ios.css
│   ├── my-card.md.css
│   ├── my-card.css
│   ├── my-card.tsx
│   └── test
│       └── basic
│           ├── e2e.js
│           └── index.html
├── my-card-content
│   ├── my-card-content.ios.css
│   ├── my-card-content.md.css
│   ├── my-card-content.css
│   └── my-card-content.tsx
├── my-card-title
│   ├── my-card-title.ios.css
│   ├── my-card-title.md.css
│   ├── my-card-title.css
```


## ネーミング
### HTMLタグ

#### Prefix
The prefix has a major role when you are creating a collection of components intended to be used across different projects, like [@ionic/core](https://www.npmjs.com/package/@ionic/core). Web Components are not scoped because they are globally declared within the webpage, which means a "unique" prefix is needed to prevent collisions. The prefix also helps to quickly identify the collection a component is part of. Additionally, web components are required to contain a "-" dash within the tag name, so using the first section to namespace your components is a natural fit.

Stencilはステンシルコンポーネントを出力しないため、プレフィックスとして「stencil」を使用することはお勧めしません。出力は標準に準拠したWebコンポーネントです。

こんなことしないで：
```markup
<stencil-component>
<stnl-component>
```

代わりに、独自の名前またはブランドを使用してください。 たとえば、[Ionic](https://ionicframework.com/)コンポーネントには、すべて「ion-」というプレフィックスが付いています。
```markup
<ion-button>
<ion-header>
```


#### 名前

コンポーネントはアクションではなく、概念的には「モノ」です。 「animating」の代わりに「animation」など、動詞の代わりに名詞を使用することをお勧めします。 「入力」、「タブ」、「ナビゲーション」、「メニュー」はいくつかの例です。


#### 修飾子

複数のコンポーネントが関連している、または結合されている場合は、名前を共有してから、次のように異なる修飾子を追加することをお勧めします。

```markup
<ion-card>
<ion-card-header>
<ion-card-content>
```


### コンポーネント（TSクラス）

クラスはスコープされているため、コンポーネントのES6クラスの名前にはプレフィックスを付けないでください。 衝突の危険はありません。

```tsx
@Component({
  tag: 'ion-button'
})
export class Button { ... }

@Component({
  tag: 'ion-menu'
})
export class Menu { ... }
```


## TypeScript

1. **Variable decorators should be inlined.**

```tsx
@Prop() name: string;
@Element() el: HTMLElement;
```

2. **Method decorator should be multi-line**

```tsx
@Listen('click')
onClick() {
  ...
}
```

3. **Use private variables and methods as much possible:** They are useful to detect dead code and enforce encapsulation. Note that this is a feature which TypeScript provides to help harden your code, but using `private`, `public` or `protected` does not make a difference in the actual JavaScript output.

4. **Code with Method/Prop/Event/Component decorators should have JSDocs:** This allows for documentation generation and for better user experience in an editor that has TypeScript intellisense

## コード編成

**ロバートC.マーチンの _クリーンコード_ からのNewspaperのメタファー**

>ソースファイルは新聞記事のように整理する必要があります。最高レベルの要約が上部にあり、詳細はさらに下にあります。一番上の関数から呼び出された関数はそのすぐ下にあり、以下同様に最下位レベルに、最も詳細な関数が一番下にあります。これは、IDEによって関数の場所の重要性が低くなったとしても、ソースコードを整理するのに適した方法です。これは、関数の出入りが非常に簡単だからです。

### 高レベルの例（コメント付き）

```tsx
@Component({
  tag: 'ion-something',
  styleUrls: {
    ios: 'something.ios.css',
    md: 'something.md.css',
    wp: 'something.wp.css'
  }
})
export class Something {

  /**
   * 1. Own Properties
   * Always set the type if a default value has not
   * been set. If a default value is being set, then type
   * is already inferred. List the own properties in
   * alphabetical order. Note that because these properties
   * do not have the @Prop() decorator, they will not be exposed
   * publicly on the host element, but only used internally.
   */
  num: number;
  someText = 'default';

  /**
   * 2. Reference to host HTML element.
   * Inlined decorator
   */
  @Element() el: HTMLElement;

  /**
   * 3. State() variables
   * Inlined decorator, alphabetical order.
   */
  @State() isValidated: boolean;
  @State() status = 0;

  /**
   * 4. Public Property API
   * Inlined decorator, alphabetical order. These are
   * different than "own properties" in that public props
   * are exposed as properties and attributes on the host element.
   * Requires JSDocs for public API documentation.
   */
  @Prop() content: string;
  @Prop() enabled: boolean;
  @Prop() menuId: string;
  @Prop() type = 'overlay';

  /**
   * Prop lifecycle events SHOULD go just behind the Prop they listen to.
   * This makes sense since both statements are strongly connected.
   * - If renaming the instance variable name you must also update the name in @Watch()
   * - Code is easier to follow and maintain.
   */
  @Prop() swipeEnabled = true;

  @Watch('swipeEnabled')
  swipeEnabledChanged(newSwipeEnabled: boolean, oldSwipeEnabled: boolean) {
    this.updateState();
  }

  /**
   * 5. Events section
   * Inlined decorator, alphabetical order.
   * Requires JSDocs for public API documentation.
   */
  @Event() ionClose: EventEmitter;
  @Event() ionDrag: EventEmitter;
  @Event() ionOpen: EventEmitter;

  /**
   * 6. Component lifecycle events
   * Ordered by their natural call order, for example
   * WillLoad should go before DidLoad.
   */
  connectedCallback() {}
  disconnectedCallback() {}
  componentWillLoad() {}
  componentDidLoad() {}
  componentWillUpdate() {}
  componentDidUpdate() {}
  componentWillRender() {}
  componentShouldRender(newVal: any, oldVal: any, propName: string) {}
  componentDidRender() {}

  /**
   * 7. Listeners
   * It is ok to place them in a different location
   * if makes more sense in the context. Recommend
   * starting a listener method with "on".
   * Always use two lines.
   */
  @Listen('click', { enabled: false })
  onClick(ev: UIEvent) {
    console.log('hi!')
  }

  /**
   * 8. Public methods API
   * These methods are exposed on the host element.
   * Always use two lines.
   * Public Methods must be async.
   * Requires JSDocs for public API documentation.
   */
  @Method()
  async open(): Promise<boolean> {
    // ...
    return true;
  }

  @Method()
  async close(): Promise<void> {
    // ...
  }

  /**
   * 9. Local methods
   * Internal business logic. These methods cannot be
   * called from the host element.
   */
  prepareAnimation(): Promise<void> {
    // ...
  }

  updateState() {
    // ...
  }

  /**
   * 10. render() function
   * Always the last public method in the class.
   * If private methods present, they are below public methods.
   */
  render() {
    return (
      <Host
        attribute="navigation"
        side={this.isRightSide ? 'right' : 'left'}
        type={this.type}
        class={{
          'something-is-animating': this.isAnimating
        }}
      >
        <div class='menu-inner page-inner'>
          <slot></slot>
        </div>
      </Host>
    );
  }
}
```
