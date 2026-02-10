# Git 運用ガイド（git flow 風・コマンド無し）

このドキュメントは、本プロジェクトの **Git 運用ルールの提案書**です。ブランチモデルは git-flow を参考にしつつ、**git-flow コマンドや SourceTree 固有の操作には依存せず**、通常の Git 操作と PR 運用を前提とした「git flow 風」の運用を採用します。

初心者が多いチームでも事故が起きにくく、仕組みを理解しながら運用できることを目的としています。

---

## 目次

1. [運用方針（CLI 中心 / SourceTree は見る専）](#1-運用方針cli-中心--sourcetree-は見る専)
2. [準備](#2-準備)
3. [よく使うコマンド集（コピペ OK）](#3-よく使うコマンド集コピペok)
4. [運用ルール（git flow 風）](#4-運用ルールgit-flow-風コマンド無し)
5. [トラブル時の基本方針](#5-トラブル時の基本方針初心者向け)
6. [SourceTree の使い方（任意）](#6-sourcetree-の使い方履歴確認差分確認用--任意)

---

## 1. 運用方針（CLI 中心 / SourceTree は見る専）

本プロジェクトでは、AI 運用やチーム標準化を見据えて **ターミナル（CLI）を中心に Git 操作を行う**方針とします。

SourceTree は履歴や差分を視覚的に確認できるため、**「履歴確認・差分確認（見る専）」としての利用を推奨**します。

### なぜ CLI 中心なのか

- AI に依頼する場合、コマンドベースの方が手順を再現しやすい
- トラブル時に「何が起きているか」を理解しやすい
- GUI に依存せず、環境が変わっても同じ運用ができる

> CLI 運用＝難しい運用ではありません。最低限のコマンドを覚えれば十分に回せます。

---

## 2. 準備

### 2.1. Git のインストール

SourceTree も内部で Git を使うため、まず Git 本体を PC にインストールします。

1. Git 公式サイトにアクセスする
2. OS（Windows / macOS）に合ったインストーラーをダウンロードし、画面の指示に従ってインストールする
   - 特に設定を変更する必要はなく、デフォルトのままで問題ありません

### 2.2. ターミナル（CLI）の準備

Git の操作はターミナル（コマンド入力画面）で行います。

| OS | 利用するもの |
| :--- | :--- |
| macOS | 標準の「ターミナル」アプリ |
| Windows | PowerShell / Windows Terminal（推奨） |

> 可能であれば、開発用の統一として **VSCode のターミナル**を利用するのがおすすめです。

### 2.3. SourceTree のインストール（任意）

SourceTree は Git の履歴・差分を視覚的に確認できる GUI ツールです。本プロジェクトでは「見る専」としての利用を推奨します。

1. SourceTree 公式サイトからインストーラーをダウンロードしてインストールする
2. 初回起動時に Atlassian アカウントの登録が求められます。画面の指示に従って作成またはログインする
3. インストール後、GitHub / Bitbucket などのアカウントを連携しておくと便利です
   - Windows：`ツール > オプション > 認証`
   - macOS：`SourceTree > 環境設定 > アカウント`

---

## 3. よく使うコマンド集（コピペ OK）

この章は、**よくある作業を用途ごとに整理し、コピペできる形でまとめたもの**です。

> 基本的にはこの章のコマンドをそのままコピーして実行すれば OK です。迷った場合は自己判断で別のコマンドを打たず、担当者に確認してください。

### 3.1. リポジトリをクローンする

```bash
git clone <リポジトリURL>
cd <プロジェクトフォルダ>
```

### 3.2. 作業を始める（feature ブランチ作成）

```bash
git checkout develop
git pull
git checkout -b feature/<作業内容>
```

例：`git checkout -b feature/header-fix`

### 3.3. 変更をコミットして push する

```bash
git status
# 変更内容を確認してから追加（基本は必要なファイルだけ）
# 例：git add path/to/file1 path/to/file2

git add .

git commit -m "fix: 〇〇を修正"
# 初回 push は -u を付ける（2 回目以降は不要）
git push -u origin feature/<作業内容>
```

> `git add .` は便利ですが、意図しないファイルまで含むことがあります。基本は `git status` で確認し、可能なら `git add <ファイル>` で必要なものだけ追加してください。

### 3.4. 最新の変更を取り込む（pull）

作業前や、他メンバーの変更を取り込みたいときに実行します。

```bash
git checkout develop
git pull
```

### 3.5. 作業中の feature に develop の変更を取り込む

他メンバーの変更を取り込み、コンフリクトを早めに解消したい場合に使います。

```bash
git checkout feature/<作業内容>

git checkout develop
git pull

git checkout feature/<作業内容>
git merge develop
```

> rebase は履歴が書き換わるため、初心者が多い間は原則使いません。

### 3.6. ブランチや状態を確認する

```bash
git status
git branch
git log --oneline --decorate -5
```

### 3.7. PR を作成する（GitHub / GitLab）

push した後、GitHub / GitLab の画面から PR を作成します。

| 項目 | 内容 |
| :--- | :--- |
| マージ先 | `develop` |
| マージ方式 | 原則 **Squash merge** |

**PR に書くこと（テンプレ）**

| 項目 | 記載内容 |
| :--- | :--- |
| 目的 | 何のための変更か |
| 変更点 | 何をどう変えたか |
| 動作確認 | 何を確認したか |
| 影響範囲 | 他に影響する箇所があれば |

> **Squash merge**：PR 内の複数コミットを 1 つにまとめて `develop` に取り込みます。履歴が散らかりにくく、初心者でも運用しやすくなります。

---

## 4. 運用ルール（git flow 風・コマンド無し）

本プロジェクトでは、ブランチ運用モデルとして **git flow 風**の運用を採用します。

### 4.1. git flow 風とは

git-flow の **ブランチ役割（main / develop / feature / release / hotfix）** を採用しつつ、`git flow feature start` などの専用コマンドは使いません。

実際の作業は、通常の Git コマンド（checkout / commit / merge）と **Pull Request（PR）運用** を中心に行います。

**メリット**

- Git の基本操作の理解が積み上がる
- 例外時（hotfix・戻しマージ）にブラックボックス化しにくい
- チームごとの運用調整がしやすい

### 4.2. ブランチの種類と役割

| ブランチ名 | 役割 |
| :--- | :--- |
| `main` | 本番環境にリリースされる、最も安定したコードを管理。**直接コミット・直 push は禁止**（必ず PR） |
| `develop` | 次のリリースに向けた開発のベース。feature の統合先。開発完了した機能をここにマージする |
| `feature/*` | 新機能・タスク用。`develop` から作成し、完了後は `develop` にマージ（例：`feature/login-function`） |
| `release/*` | リリース準備用。`develop` から作成。完了後は `main` と `develop` にマージ（例：`release/v1.1.0`） |
| `hotfix/*` | 本番の緊急バグ修正用。`main` から作成。完了後は `main` と `develop` にマージ（例：`hotfix/critical-bug`） |

### 4.3. 基本的な開発フロー（新機能）

1. `develop` を最新にする
2. `develop` から `feature/*` を作成する
3. `feature/*` で作業・コミットする
4. 作業完了後、`feature/*` をリモートに push する
5. `develop` 向けの PR を作成する
6. レビュー後、原則 **Squash merge** で `develop` にマージする

### 4.4. release ブランチの運用方針

- `release/*` は **必要なときだけ** 作成する（常用しない）
- 切るタイミングは「リリース前日または当日朝」を基本とする
- release 期間中は、原則として新規 feature は追加しない
- 修正内容はバグ修正・文言修正に限定する

**リリース完了時の手順**

1. `release/*` → `main` を **Merge commit** でマージする
2. `vX.Y.Z` のタグを付与する
3. `main` → `develop` に戻しマージする（必須）

**参考：CLI で行う場合**

```bash
git checkout main
git pull
git merge --no-ff release/<version>

# タグ付与（vX.Y.Z を実際のバージョンに置き換える。例：v1.2.3）
git tag vX.Y.Z
git push origin main --tags

git checkout develop
git pull
git merge main
git push origin develop
```

### 4.5. hotfix ブランチの運用方針

- `hotfix/*` は `main` から作成する
- 修正完了後は、**必ず `main` → `develop` の順で反映**する
- hotfix もリリースとして扱い、必要に応じてタグを付与する

**参考：CLI で行う場合**

```bash
git checkout main
git pull
git merge --no-ff hotfix/<name>

# 必要に応じてタグ付与（vX.Y.Z を実際のバージョンに置き換える）
git tag vX.Y.Z
git push origin main --tags

git checkout develop
git pull
git merge main
git push origin develop
```

### 4.6. バージョン番号のルール

リリース時のバージョンは **セマンティックバージョニング** に沿い、`vX.Y.Z` の形式で管理します。

| 桁 | 例 | 上げるタイミング |
| :--- | :--- | :--- |
| **X**（メジャー） | `vX.0.0` | 大規模な仕様変更、後方互換性のない変更、リニューアルなど |
| **Y**（マイナー） | `v1.X.0` | 機能追加、中規模な仕様変更など、後方互換性のある変更 |
| **Z**（パッチ） | `v1.0.X` | バグ修正など、軽微な修正 |

`release/*` や `hotfix/*` を作成する際は、このルールに基づいてバージョン名を決めます。

---

## 5. トラブル時の基本方針（初心者向け）

### 5.1. 困ったら最初にやること

次のコマンドで状況を確認します。

```bash
git status
git branch
git log --oneline --decorate -5
```

| 確認すること |
| :--- |
| 今どのブランチにいるか |
| 何が変更されているか |
| 直前に何をしたか |

### 5.2. やってはいけないこと（重要）

- **`main` / `develop` に直接コミット・直 push しない**（必ず PR）
- **`git push -f`（force push）は原則禁止**（必要な場合は担当者に相談）
- **迷った状態で `rebase` や `reset --hard` を実行しない**

### 5.3. マージがうまくいかないとき

**マージを中止したい場合**

```bash
git merge --abort
```

**コンフリクトが出た場合**

1. どのファイルが衝突しているか確認する（`git status`）
2. 該当ファイルを編集し、`<<<<<<<` / `=======` / `>>>>>>>` のマーカーを解消する
3. `git add <ファイル>` → `git commit` で完了

> 不安な場合は、SourceTree の履歴表示で状況を見ながら整理してください。

---

## 6. SourceTree の使い方（履歴確認・差分確認用 / 任意）

### 6.1. リポジトリのクローン

1. SourceTree を起動し、`+` ボタン > `Clone` を選択する
2. 「ソースパス/URL」にリモートリポジトリの URL を貼り付ける
3. 「保存先のパス」に、ローカルで管理したいフォルダを指定する
4. `クローン` ボタンをクリックする

※ 通常の開発では 3.1 の CLI でクローンして構いません。

### 6.2. 変更内容の確認

SourceTree では主に以下を確認します。

- 変更ファイルの一覧
- 差分（diff）
- ブランチの状態

push / merge / ブランチ作成などの操作は、原則 CLI で行います。

### 6.3. 履歴・ブランチの確認

履歴画面で以下を視覚的に確認できます。

- ブランチの分岐の仕方
- マージの履歴
- コンフリクトが発生していそうな箇所

困ったときは、SourceTree で履歴を見ながら状況を整理するのがおすすめです。
