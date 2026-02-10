
# SCSS構成・運用ルール  v1.0.0

本テーマのCSSは、**保守性・再利用性・属人化防止**を目的として設計されています。
非エンジニアや将来の担当者、AIアシスタントが迷わず安全に運用できるよう、以下のルールに従ってください。

---

## 要点まとめ（まずここだけ読めばOK）

このドキュメントは、**保守性・再利用性・属人化防止**を目的とした SCSS 設計・運用方針の提案書です。

### 設計の前提
- SCSS で管理し、直接編集するのは `scss/` 配下のみ
- FLOCSS ベース（foundation / layout / object）で構成
- **Component（c-）中心設計**。Page や Layout から依存しない

### ディレクトリの考え方
- **foundation**：変数・関数・mixin・reset・タグスタイルなど設計の土台（役割クラスは置かない）
- **layout**：ヘッダー・フッターなどサイト構造
- **object/component**：再利用可能な UI 部品
- **object/page**：ページ固有の調整
- **object/utility**：設計を壊さずに微調整するための最終手段

### 単位・デザイン変換のルール
- px 指定はなるべく避け、**原則 rem ベース**で記述
- フォントサイズ：`fluid-font-rem()`
- 余白・レイアウト幅：**振り幅がある場合のみ** `fluid-space-rem()`
- 境界線（1px）など、px が適切なケースは許容

### レスポンシブ方針
- **モバイルファースト**（SP を初期スタイル）
- Figma デザインは PC 基準でも、CSS は SP 向けに整理して実装
- PC 差分のみをメディアクエリで追加

### hover 設計の要点
- hover を検知する要素：`.c-link`
- 見た目が変化する要素：`.c-link__target`
- HTML 構造が変わっても壊れない設計を重視

### AI に依頼する場合
- エントリポイント（`style.scss` / `editor.scss`）には直書きしない
- `_index.scss` + `@forward` を基本とする
- 本ドキュメントの設計思想に従うよう明示する

---

---

## 目次

1. [CSS設計思想](#1-css設計思想)
2. [新しくスタイルを追加する手順](#2-新しくスタイルを追加する手順)
3. [命名ルール（接頭辞）](#3-命名ルール接頭辞)
4. [Figma → SCSS 変換ルール](#4-figma--scss-変換ルール)
5. [レスポンシブのルール](#5-レスポンシブのルール)
6. [リンク・ボタンの hover 設計](#6-リンクボタンの-hover-設計について非エンジニア向け)
7. [AIアシスタントへの指示ルール](#7-aiアシスタントへの指示ルール)

---

## 1. CSS設計思想

- **SCSSで管理**：出力は `style.css` ですが、編集は必ず `scss/` ディレクトリ内のファイルで行います。
- **FLOCSSベース**：役割ごとに `foundation` / `layout` / `object` の3階層に分けています。
- **1ファイル1責務**：「どこに何が書いてあるか」をファイル名から明確にします。

### ディレクトリ構成

```text
scss/
├─ style.scss        // フロント用エントリポイント
├─ editor.scss       // 管理画面用エントリポイント
├─ foundation/       // 土台（変数/関数/mixin/reset/タグスタイル等。c-/l-/p-/u-など役割クラスは置かない）
├─ layout/           // 枠組み（ヘッダー・フッター・サイドバーなど）
└─ object/
   ├─ component/     // 部品（ボタン・カード・見出しなど）
   ├─ page/          // ページ固有（トップページ・記事詳細など）
   └─ utility/       // 調整（余白・表示制御など）
```

---

## 2. 新しくスタイルを追加する手順

迷ったら以下の優先順位で置き場所を決めてください。

| 優先順位 | 種類 | ディレクトリ | 役割 |
| :--- | :--- | :--- | :--- |
| **1** | **共通UI** | `object/component` | 他のページでも使い回せる部品（ボタン、カード等） |
| **2** | **ページ専用** | `object/page` | そのページでしか使わない独自の装飾 |
| **3** | **レイアウト** | `layout` | サイト共通のヘッダー・フッター・枠組み |
| **4** | **調整** | `object/utility` | たった1箇所の微調整や余白調整（※最終手段として用意する） |

### 作業ステップ

1. **ファイルを新規作成する**  
   例：`object/component/_card.scss`
2. **`_index.scss` に登録する**  
   作成した階層の `_index.scss` を開き、行を追加します。  
   例：`object/component/_index.scss` に `@forward "component/card";`
3. **完了**  
   `style.scss` を編集する必要はありません（自動的に読み込まれます）。

---

## 3. 命名ルール（接頭辞）

クラス名の先頭に、役割を示すプレフィックスを付けます。

| 接頭辞 | 意味 | 使用例 |
| :--- | :--- | :--- |
| **l-** | Layout | `.l-header`, `.l-main` |
| **c-** | Component | `.c-btn`, `.c-card` |
| **p-** | Page | `.p-home`, `.p-contact` |
| **u-** | Utility | `.u-mt-10`, `.u-text-center` |

---

## 4. Figma → SCSS 変換ルール

Figmaのデザインをコードに落とし込む際のルールです。

### フォントサイズ・余白

px指定はなるべく避け、**原則として rem ベースで記述**します。

- フォントサイズ：`fluid-font-rem()` を使用
- 余白・レイアウト幅（領域）：`fluid-space-rem()` を使用
- 境界線（1px）など、デザイン上 px が適切なケースは許容

Figmaの数値（px）は以下の表を参考に変換してください。

| 項目 | Figma(px) | SCSS記述例 | 備考 |
| :--- | :--- | :--- | :--- |
| 文字サイズ | 16px | `fluid-font-rem(1, 1)` | 16px = 1rem |
| 文字サイズ | 24px | `fluid-font-rem(1.5, 2)` | SP/PCで可変対応 |
| 余白 | 24px | `padding: fluid-space-rem(1.5, 2);` | `fluid-space-rem` を使用 |

### 色（カラー）

`#333` などのカラーコードを直接書かず、`foundation/_color.scss` に定義された CSS 変数を使用してください。

```scss
/* NG */
color: #333;
background-color: #fff;

/* OK */
color: var(--c-black);
background-color: var(--c-white);
```

---

## 5. レスポンシブのルール

レスポンシブ対応は**スマホファースト（モバイル基準）**で実装してください。

> **補足**  
> 実案件では Figma デザインが PC 基準で提供されることが多いですが、CSS 実装時は PC デザインをそのまま書かず、SP 向けに情報量・レイアウトを整理した状態を「初期スタイル」とします。PC 向けの差分のみをメディアクエリで追加し、モバイルファーストを維持します。

### 基本方針

- **初期状態 = スマホ（SP）向けのスタイル**
- タブレット・PC 向けは `@include tb` などのメディアクエリで上書き
- PC 用スタイルをベースに書き、SP で打ち消す書き方は避ける（上書きが増え保守性が下がるため）

**メリット**

- スタイルの見通しが良くなる
- 不要な上書きが減り、保守性が向上する
- 新しいデバイスサイズにも対応しやすい

### 記述ルール

1. SP 用スタイルを通常の CSS として記述する
2. 画面が広くなる条件のみメディアクエリで追加する

### 記述例

```scss
.c-archive-list {
   display: flex;
   flex-direction: column;
   gap: fluid-space-rem(1, 1.5);
   li {
      border: 1px solid var(--c-blue);
   }

   // タブレット以上
   @include tb {
      flex-direction: row;
      flex-wrap: wrap;

      li {
         width: calc((100% - (4 * #{fluid-space-rem(1, 1.5)})) / 5);
      }
   }
}
```

---

## 6. リンク・ボタンの hover 設計について（非エンジニア向け）

リンクやボタンの hover 表現を、次の2つに分けて設計しています。

| 役割 | 説明 |
| :--- | :--- |
| **起点** | マウスを乗せたことを検知する場所 |
| **変化点** | 色や下線など、見た目が変わる場所 |

この分離により、デザインを安全に使い回せます。

### なぜ分けているのか

同じリンク表現でも「テキストだけのリンク」「カード全体がクリックできるリンク」など使われ方が異なります。役割を分けておくことで、

- デザインの再利用がしやすい
- 一部を変更しても他に影響しにくい
- 将来の追加や修正が安全

というメリットがあります。

### 実装上の考え方

- hover の反応は「起点」となる要素が担当する
- 色や下線などの見た目は「変化する要素」にまとめる
- HTML の並び順ではなく、役割ごとのクラスで制御する

### hover 実装例（2パターン）

役割は次のクラスで分けます。

- **hover を検知する要素**：`.c-link`
- **見た目が変化する要素**：`.c-link__target`

責務を分けることで、HTML 構造が変わっても一貫した hover 運用ができます。

#### パターン①：テキストリンク（a タグ単体）

```html
<a href="#" class="c-link c-link__target">
  <span class="c-link__text">詳しく見る</span>
  <span class="c-icon">→</span>
</a>
```

- hover 検知（`.c-link`）と変化点（`.c-link__target`）が**同一要素**
- シンプルなリンク向け

#### パターン②：カード全体リンク（中の一部だけ変化）

```html
<a href="#" class="c-link">
  <div class="image"><img src="/img/noimg.jpg" alt=""></div>
  <div class="content">
    <div class="c-link__target">
      <span class="c-link__text">詳しく見る</span>
      <svg class="c-icon"><use href="#icon-arrow-right"></use></svg>
    </div>
  </div>
</a>
```

- `.c-link` がカード全体の hover を検知
- `.c-link__target` が見た目の変化のみを担当
- hover 範囲と見た目変更範囲を分離できる

#### SCSS 例（共通）

```scss
.c-link {
   /* hoverの起点は常に .c-link */
}

.c-link__target {
   display: flex;
   align-items: center;

   // PC/SPで値が変わらない場合は、fluid関数を使わず rem で記述
   gap: 0.5rem;

   width: 100%;
   text-decoration: none;

   font-size: fluid-font-rem(1, 1.25);
   line-height: 1.5;
   letter-spacing: 0.08em;

   color: var(--c-black);
   transition: color $base_ani;

   .c-icon {
      flex-shrink: 0;
      width: 1em;
      color: var(--c-accent);
   }
}

.c-link[href]:hover .c-link__target,
.c-link.c-link__target[href]:hover {
   color: var(--c-accent);
}
```

**まとめ**：hover 検知は `.c-link`、見た目変化は `.c-link__target`。同一要素でも入れ子構造でも、役割を分けて考えることで一貫した運用が可能です。

---

## 7. AIアシスタントへの指示ルール

AI（ChatGPT, Claude, GitHub Copilot 等）にコード修正を依頼する際は、以下のプロンプトを併記すると、本プロジェクトの設計思想に沿ったコードが出力されやすくなります。

```markdown
# AIへの指示ルール
このプロジェクトのCSS設計ルールに従ってコーディングしてください。

## 重視する制約
1. **ディレクトリ構造**: FLOCSSベース（foundation / layout / object）を厳守すること。
2. **エントリポイント**: `style.scss` に直接スタイルを書かないこと。必ず部分ファイルを作成し `@use` または階層ごとの `_index.scss` で `@forward` すること。
3. **単位の扱い**: `px` 指定はなるべく避け、原則として rem ベースで記述すること。文字サイズは `fluid-font-rem()`、余白やレイアウト幅（領域）の指定は `fluid-space-rem()` 関数を使用すること。境界線（1px）など、デザイン上 px が適切なケースは許容する。
4. **命名規則**: クラス名には適切なプレフィックス（l-, c-, p-, u-）を付与すること。
5. **依存関係**: Component（c-）が Layout（l-）や Page（p-）に依存するスタイルを書かないこと。Component はどこでも配置可能にすること。
6. **関数定義**: `fluid-font-rem` / `fluid-space-rem` は `foundation/_functions.scss` を使用すること。
7. **レスポンシブ**: モバイルファーストで実装すること。SP 向けに整理したスタイルをベースとし、PC 用は `@include tb` 等のメディアクエリで差分を追加すること。PC 用スタイルを先に書き、SP で打ち消す書き方は避けること。
```
