# CSSコーディングルール
## ファイル・ディレクトリ構成
ファイルの構成はFLOCSSをベースにします。

```
root
└── assets/
    └── css/
        ├── common.scss
        ├── foundation/
        │   ├── base/
        │   ├── function/
        │   ├── mixin/
        │   └── variable/
        ├── layout/
        └── object/
        　   ├── component/
        　   ├── page/
        　   └── utility/
```

スタイルは基本的に`/assets/css/common.scss`で出力しますが、ページ特有のスタイルが多く出る場合は`css/pagename.scss`のようにページ専用のスタイルシートを作ることもできます。

無理に1つのCSSとして管理するより、以下のようにHTMLから優先度が把握できるほうがスタイルの追加が容易になり、`common.css`の肥大化・複雑化を防ぐことができます。

```html
<link rel="stylesheet" href="/assets/css/common.css">
<link rel="stylesheet" href="/css/index.css">
```

### FLOCSSの基本
FLOCSSはFoundation、Layout、Objectの3つのレイヤーとComponent、Page、Utilityの3つの子レイヤーから構成されます。

#### 1. Foundation
Foundationレイヤーでは`html`や`body`のような広範囲にわたるベーススタイル、`h2`や`ul`のような基本的なタイプセレクタのデフォルトスタイルを定義します。装飾的なスタイルは避けて、できる限り低詳細度に保ちます。idセレクタやclassセレクタは使用しません。

Foundationレイヤーにはレイヤーを追加するため、normalize.cssやベーススタイルは`foundation/base`レイヤーに指定します。

```scss
@import "foundation/base/_normalize";
@import "foundation/base/_base";
```

  #### 2. Layout
Layoutレイヤーはヘッダーやフッターのような、ページを構成するコンテナブロックのスタイルを定義します。目安としてはワイヤーフレームに書かれるような大きな単位のブロックです。

例えば、ヘッダーやフッターなど

プレフィックス（接頭辞）として`l-`をつけます。

```scss
@import "layout/_header";
@import "layout/_footer";
@import "layout/_kv";
@import "layout/_background";
@import "layout/_main";
```

#### 3. Object
Objectレイヤーはプロジェクトにおけるビジュアルパターンです。抽象度や詳細度、拡張性などによって、3つのレイヤーにわけられます。

1. Component（`c-`）
2. Page（`p-`）
3. Utility（`u-`）

#### 3.1 Component
複数のページで使い回せるようなコンポーネントです。

プレフィックス（接頭辞）として`c-`をつけます。

```scss
@import "object/component/_icon";
@import "object/component/_label";
@import "object/component/_button";
@import "object/component/_headline";
```

#### 3.2 Page
ページ特有のスタイルを記述します。

ページ内でのみ繰り返し使われるスタイルもここに記述し、プレフィックス（接頭辞）として`s-`（スコープ）をつけます。


```scss
@import "object/page/_top";
@import "object/page/_about";

```

#### 3.4 Utility
Utilityレイヤーはいわゆる汎用クラスで、ほとんどの場合は単一のスタイルをもっています。コンポーネント間の間隔を調整したり、BEMのModifierが増えすぎるのを防ぎます。

`.mb10`のような具体的な名前より`.mb-small`のような相対的な名前にしたり、pxよりもemや%で指定することを推奨します。確実にスタイルを適応させるために`!important`を使用します。

プレフィックス（接頭辞）として`u-`をつけます。

```scss
@import "object/utility/_text";
@import "object/utility/_align";
@import "object/utility/_display";
@import "object/utility/_margin";
```

### 追加するレイヤー
FLOCSSのファイル構成に4つのレイヤーを標準で追加します。

#### 1.1 Variable
プロジェクト全体で使われる変数を定義します。例えば以下のように、基本的な変数を定義する`global.scss`とブレイクポイント、色、font-familyを定義するファイルを分割すると見通しがよくなります。

```scss
@import "foundation/variable/_global";
@import "foundation/variable/_breakpoint";
@import "foundation/variable/_font-family";
@import "foundation/variable/_color";
```

Sass  で変数を定義する場合は以下のことに気をつけます。

- `!default`フラグを指定することで、先に読み込んだ変数が適応されるルールに統一します。
- `$pn-`(Project name)のようにプレフィックスを付けることで名前の衝突を防ぎます。
- ハイフン（`-`）とアンダースコア（`_`）は同一と見なされるので、注意して扱います。
- 3箇所以上で使われる共通の値やプロジェクトに関わらず利用できる値だけを定義します。

#### 1.2 Function
Sassで使用できるfunctionを機能ごとに定義します。例えばpxをremに変換するfunctionなどです。

```scss
@import "foundation/function/_em";
@import "foundation/function/_rem";
```

#### 1.3 Mixin
Sassで使用できるmixinを機能ごとに定義します。例えばメディアクエリやclearfix、再利用できるオブジェクトのベーススタイルなどです。

```scss
@import "foundation/mixin/_mq-up";
@import "foundation/mixin/_mq-down";
@import "foundation/mixin/_clearfix";
```

#### 1.4 Base
normalize.cssや要素セレクタ・属性セレクタのデフォルトスタイルを指定します。

```scss
@import "foundation/base/_normalize";
@import "foundation/base/_base";
```

#### モジュール化
Layoutレイヤー以下のモジュールは機能ごとにファイルに分割します。モジュールは後述するBEMをベースに名前をつけ、モジュール名とファイル名は一致するようにします。

- `.grid` => `_grid.scss`
- `.button` => `_button.scss`

#### レイヤーの順序
カスケーディングを管理するため、レイヤーを読み込ませる順序は

- 低詳細度から高詳細度
- 抽象的なスタイルから具体的なスタイル
- カスケーディングを許容するモジュールからカスケーディングを許容しないモジュール

のようにする必要があります。例えば以下のような順番で読み込ませます。

```scss
@import "foundation/";
@import "layout/";
@import "object/component/";
@import "object/project/";
@import "object/page/";
@import "object/utility/";
```

## 命名規則
命名規則はBEM（MindBEMding）をベースとし、レイヤーごとにプレフィックスをつけることで名前空間とし、ブレイクポイントをもつモジュールにはサフィックス（接尾辞）をつけることで名前のブレを防ぎます。

### BEM（MindBEMding）
[BEM](https://github.com/juno/bem-methodology-ja/blob/master/definitions.md)をベースにした[MindBEMding](http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/)を使用します。BEMには役割や関係性を明確にできるメリットがあります。

BEMはBlock、Element、Modifierの3つの概念から構成され、以下のようなクラス名になります。

```scss
.block-name {}
.block-name__element {}
.block-name--modifier-name {}
.block-name__element--modifier-name {}
```

* 小文字の英単語とハイフン1つ（`-`）とハイフン2つ（`--`）、アンダースコア2つ（`__`）で構成されます
* BlockとElementとModifierの単語はハイフン（`-`）でつなぎます
* BlockとModifierはできるだけ2つ以上の単語を合わせてユニークな名前にします
* ElementとModifierは1つの単語だけを使ってもかまいません
* ElementとModifierはBlockの名前を受け継ぎます
* BlockとElementはアンダースコア2つ（`__`）でつなぎます
* BlockとModiferもしくはElementとModiferはハイフン2つ（`--`）でつなぎます

基本的にクラスセレクタ1つだけにスタイルを指定しますが、`.block--modifier`の指定でElementにもスタイルを指定したい場合にはクラスセレクタを2つ使って指定することもできます。ただし、直近の子要素に限定するなど、影響範囲をできるだけ狭くしておきます。

```scss
// Good
.block--modifier > .block__element {}

// Bad
.block--modifier .block__element {}
```

### プレフィックス（Prefix）
レイヤーごとの役割を示したり、名前の重複をさせないために名前空間としてプレフィックスをつけます。FLOCSSで推奨されているプレフィックスといくつかのプレフィックスを使用します。

- `l-` Layoutレイヤー
- `c-` Componentレイヤー
- `s-` Page内のScopeレイヤー
- `u-` Utilityレイヤー
- `is-` クリックやマウスオーバーなどのイベントが発生している要素に付与する
- `js-` JavaScriptから参照される要素

### サフィックス（Suffix）
ブレイクポイントを指定しているクラス名は`-sm`や`-md`のようなブレイクポイントのキーワードをクラス名の末尾につけます。
ブレイクポイントはグローバル変数として定義しておき、そのキーをクラス名にも使うようにします。

```scss
$_breakpoint-up: (
  'sm': 'screen and (min-width: 400px)',
  'md': 'screen and (min-width: 768px)',
  'lg': 'screen and (min-width: 1000px)',
  'xl': 'screen and (min-width: 1200px)',
) !default;
```

```scss
@media (min-width: 400px) {
  .u-dn-sm { display: none; }
}

@media (min-width: 768px) {
  .u-dn-md { display: none; }
}

@media (min-width: 1000px) {
  .u-dn-lg { display: none; }
}

@media (min-width: 1200px) {
  .u-dn-xl { display: none; }
}
```

メディアクエリは`min-width`を優先的に使い`-sm`などとします。`max-width`のパターンも作る場合は`-sm-down`などとします。

### 注意事項とTODO
あとで処理をしたいことや、把握しておいてほしい注意事項はコメントに残していつでも検索できるようにします。

- `NOTE:` コード上で確認できない制限などの共有
- `TBD:` 今後どうしていくか検討したいこと
- `TODO:` 動作はするけれど改修したい箇所

