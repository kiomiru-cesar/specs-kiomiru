# Git 運用ガイド（git flow風・コマンド無し）

## 1. はじめに

このドキュメントは、本プロジェクトにおける **Git運用ルールの提案書** です。
ブランチモデルは git-flow を参考にしますが、**git-flow コマンドや SourceTree 固有の操作には依存せず**、通常の Git 操作と PR 運用を前提とした「git flow風」の運用を採用します。

初心者が多いチームでも事故が起きにくく、仕組みを理解しながら運用できることを目的としています。

## 2. 目次

- 3 運用方針（CLI中心 / SourceTreeは見る専）
- 4 準備
  - 4.1. Gitのインストール
  - 4.2. ターミナル（CLI）の準備
  - 4.3. SourceTreeのインストール（任意）
- 5 よく使うコマンド集（コピペOK）
  - 5.1. リポジトリをクローンする
  - 5.2. 作業を始める（featureブランチ作成）
  - 5.3. 変更をコミットしてpushする
  - 5.4. 最新の変更を取り込む（pull）
  - 5.5. 作業中のfeatureに develop の変更を取り込む
  - 5.6. ブランチや状態を確認する
  - 5.7. PRを作成する（GitHub/GitLab）
- 6 運用ルール（git flow風・コマンド無し）
  - 6.1. git flow風とは
  - 6.2. ブランチの種類と役割
  - 6.3. 基本的な開発フロー（新機能）
  - 6.4. release ブランチの運用方針
  - 6.5. hotfix ブランチの運用方針
  - 6.6. バージョン番号のルール
- 7 トラブル時の基本方針（初心者向け）
  - 7.1. 困ったら最初にやること
  - 7.2. やってはいけないこと（重要）
  - 7.3. マージがうまくいかないとき
- 8 SourceTreeの使い方（履歴確認・差分確認用 / 任意）
  - 8.1. リポジトリのクローン
  - 8.2. 変更内容の確認
  - 8.3. 履歴・ブランチの確認

## 3. 運用方針（CLI中心 / SourceTreeは見る専）

本プロジェクトでは、今後のAI運用やチーム標準化を見据えて、**ターミナル（CLI）を中心にGit操作を行う**方針とします。

SourceTree は視覚的に履歴や差分を確認できるメリットがあるため、
**「履歴確認・差分確認（見る専）」として利用することを推奨**します。

### なぜCLI中心なのか

- AIに依頼する場合、コマンドベースの方が手順を再現しやすい
- トラブル時に「何が起きているか」を理解しやすい
- GUI固有の操作に依存せず、環境が変わっても同じ運用ができる

※ CLI運用＝難しい運用ではありません。最低限のコマンドを覚えれば十分に回せます。

## 4. 準備

### 4.1. Gitのインストール

SourceTreeは内部でGitを使用するため、まずGit本体をPCにインストールする必要があります。

1.  Git公式サイトにアクセスします。
2.  お使いのOS（Windows, macOS）に合ったインストーラーをダウンロードし、画面の指示に従ってインストールしてください。
    *   特に設定を変更する必要はなく、基本的にデフォルトのままで問題ありません。

### 4.2. ターミナル（CLI）の準備

Git の操作は、ターミナル（コマンド入力画面）で行います。

- macOS：標準の「ターミナル」アプリ
- Windows：PowerShell / Windows Terminal（推奨）

※ 可能であれば、開発用の統一として **VSCode のターミナル機能** を利用するのがおすすめです。

### 4.3. SourceTreeのインストール（任意）

SourceTree は Git の履歴や差分を視覚的に確認できるGUIツールです。本プロジェクトでは「見る専（履歴確認・差分確認）」としての利用を推奨します。

1.  SourceTree公式サイトにアクセスし、インストーラーをダウンロードしてインストールします。
2.  初回起動時にAtlassianアカウントでの登録が求められます。画面の指示に従ってアカウントを作成またはログインしてください。
3.  インストール後、リモートリポジトリ（GitHub, Bitbucketなど）のアカウントを連携設定しておくと、リポジトリへのアクセスがスムーズになります。
    *   `ツール > オプション > 認証` (Windows)
    *   `SourceTree > 環境設定 > アカウント` (macOS)

## 5. よく使うコマンド集（コピペOK）

この章は、初心者や非エンジニアの方でも迷わず作業できるように、
**よくある作業を「用途」ごとに整理し、コピペできる形でまとめたもの**です。

> ※ 基本的には、この章のコマンドをそのままコピーして実行すればOKです。
> ※ 迷った場合は自己判断で別のコマンドを打たず、担当者に確認してください。

### 5.1. リポジトリをクローンする

```bash
git clone <リポジトリURL>
cd <プロジェクトフォルダ>
```

### 5.2. 作業を始める（featureブランチ作成）

```bash
git checkout develop
git pull
git checkout -b feature/<作業内容>
```

例：

```bash
git checkout -b feature/header-fix
```

### 5.3. 変更をコミットしてpushする

```bash
git status
# まずは変更内容を確認してから追加します（基本は必要なファイルだけ）
# 例：git add path/to/file1 path/to/file2

git add .

git commit -m "fix: 〇〇を修正"
# 初回pushは -u を付けます（2回目以降は不要）
git push -u origin feature/<作業内容>
```

※ `git add .` は便利ですが、意図しないファイルまで含めてしまうことがあります。基本は `git status` で確認し、可能なら `git add <ファイル>` で必要なものだけ追加してください。

### 5.4. 最新の変更を取り込む（pull）

作業前や、他メンバーの変更を取り込みたいときに実行します。

```bash
git checkout develop
git pull
```

### 5.5. 作業中のfeatureに develop の変更を取り込む

他メンバーの変更を取り込んでコンフリクトを早めに解消したい場合に使います。

```bash
# 自分のfeatureブランチへ移動

git checkout feature/<作業内容>

# develop を最新にする

git checkout develop
git pull

# feature に取り込む（merge）

git checkout feature/<作業内容>
git merge develop
```

※ rebase は履歴が書き換わるため、初心者が多い間は原則使いません。

### 5.6. ブランチや状態を確認する

```bash
git status
git branch
git log --oneline --decorate -5
```

### 5.7. PRを作成する（GitHub/GitLab）

コマンドで push した後は、GitHub / GitLab の画面から PR を作成します。

- マージ先：`develop`
- マージ方式：原則 **Squash merge**

#### PRに書くこと（テンプレ）

- 目的：
- 変更点：
- 動作確認：
- 影響範囲：

※ Squash merge：PR内の複数コミットを1つにまとめて `develop` に取り込みます。履歴が散らかりにくく、初心者でも運用しやすくなります。

## 6. 運用ルール（git flow風・コマンド無し）

本プロジェクトでは、ブランチ運用モデルとして **git flow風** の運用を採用します。

### 6.1. git flow風とは

git-flow の **ブランチ役割（main / develop / feature / release / hotfix）** を採用しつつ、
`git flow feature start` などの専用コマンドは使用しません。

実際の作業は、通常の Git コマンド（checkout / commit / merge）と、
GitHub・GitLab などの **Pull Request（PR）運用** を中心に行います。

これにより、
- Git の基本操作の理解が積み上がる
- 例外時（hotfix・戻しマージ）にブラックボックス化しにくい
- チームごとの運用調整がしやすい

といったメリットがあります。

### 6.2. ブランチの種類と役割

| ブランチ名 | 役割 |
| :--- | :--- |
| `main` | **本番環境にリリースされる、最も安定したコード**を管理します。このブランチに直接コミット・直pushしないでください（必ずPR）。 |
| `develop` | **次のリリースに向けた開発のベースとなるブランチ**です。開発が完了した機能は、このブランチにマージされます。feature ブランチの統合先とします。 |
| `feature/*` | 新機能の開発やタスクを行うためのブランチです。（例: `feature/login-function`）`develop`から作成し、完了後は`develop`にマージします。 |
| `release/*` | リリース準備のためのブランチです。（例: `release/v1.1.0`）`develop`から作成し、リリース前のバグ修正などを行います。完了後は`main`と`develop`にマージされます。 |
| `hotfix/*` | 本番環境で発生した緊急のバグを修正するためのブランチです。（例: `hotfix/critical-bug`）`main`から作成し、修正完了後は`main`と`develop`にマージされます。 |

### 6.3. 基本的な開発フロー（新機能）

1. `develop` ブランチを最新の状態に更新します。
2. `develop` から `feature/*` ブランチを作成します。
3. `feature/*` ブランチで作業・コミットを行います。
4. 作業完了後、`feature/*` をリモートに push します。
5. `develop` 向けの Pull Request を作成します。
6. レビュー後、原則 **Squash merge** で `develop` にマージします。

### 6.4. release ブランチの運用方針

- `release/*` は **必要なときだけ** 作成します（常用しません）。
- 切るタイミングは「リリース前日または当日朝」を基本とします。
- release 期間中は、原則として新規 feature は追加しません。
- 修正内容はバグ修正・文言修正に限定します。

リリース完了時の手順：
1. `release/*` → `main` を **Merge commit** でマージ
2. `vX.Y.Z` のタグを付与
3. `main` → `develop` に戻しマージ（必須）

（参考：CLIで行う場合）

```bash
# releaseをmainへ取り込む（Merge commit）

git checkout main
git pull
git merge --no-ff release/<version>

# タグ付与（例：v1.2.3）

git tag vX.Y.Z
git push origin main --tags

# mainの変更をdevelopへ戻す

git checkout develop
git pull
git merge main
git push origin develop
```

### 6.5. hotfix ブランチの運用方針

- `hotfix/*` は `main` から作成します。
- 修正完了後は、**必ず `main` → `develop` の順で反映**します。
- hotfix もリリースとして扱い、必要に応じてタグを付与します。

（参考：CLIで行う場合）

```bash
# hotfixをmainへ取り込む

git checkout main
git pull
git merge --no-ff hotfix/<name>

# 必要に応じてタグ付与

git tag vX.Y.Z
git push origin main --tags

# mainの変更をdevelopへ戻す

git checkout develop
git pull
git merge main
git push origin develop
```

### 6.6. バージョン番号のルール

本プロジェクトでは、リリース時のバージョン番号をセマンティックバージョニングに沿って管理します。バージョンは `vX.Y.Z` の形式で表現されます。

*   **X (メジャーバージョン)**: `vX.0.0`
    *   大規模な仕様変更、後方互換性のない変更、プロジェクトのリニューアルなど。
*   **Y (マイナーバージョン)**: `v1.X.0`
    *   機能追加、中規模な仕様変更など、後方互換性のある変更。
*   **Z (パッチバージョン)**: `v1.0.X`
    *   バグ修正など、軽微な修正。

`release/*` や `hotfix/*` ブランチを作成する際は、このルールに基づいてバージョン名を決定します。

## 7. トラブル時の基本方針（初心者向け）

### 7.1. 困ったら最初にやること

```bash
git status
git branch
git log --oneline --decorate -5
```

- 何のブランチにいるか
- 何が変更されているか
- 直前に何をしたか

を確認します。

### 7.2. やってはいけないこと（重要）

- `main` / `develop` に直接コミット・直pushしない（必ずPR）
- `git push -f`（force push）は原則禁止（必要な場合は担当者に相談）
- 迷った状態で `rebase` や `reset --hard` を実行しない

### 7.3. マージがうまくいかないとき

- マージを中止したい：

```bash
git merge --abort
```

- コンフリクトが出たら：
  1) どのファイルが衝突しているか確認（`git status`）
  2) 該当ファイルを修正
  3) `git add <ファイル>` → `git commit`

※ 不安な場合は SourceTree の履歴表示で状況を見ながら整理してください。

## 8. SourceTreeの使い方（履歴確認・差分確認用 / 任意）

### 8.1. リポジトリのクローン

リモートリポジトリを自分のPCにコピー（クローン）して、開発を始める準備をします。

1.  SourceTreeを起動し、`+`ボタン > `Clone` を選択します。
2.  リモートリポジトリのURLを「ソースパス/URL」に貼り付けます。
3.  「保存先のパス」に、ローカルPC上でリポジトリを管理したいフォルダを指定します。
4.  `クローン`ボタンをクリックします。

### 8.2. 変更内容の確認

SourceTree では、主に以下を確認します。

- 変更ファイルの一覧
- 差分（diff）の確認
- ブランチの状態

※ push / merge / ブランチ作成などの操作は、原則 CLI で行います。

### 8.3. 履歴・ブランチの確認

SourceTree の履歴画面を使うと、以下を視覚的に確認できます。

- どのブランチがどこから分岐したか
- マージの履歴
- コンフリクトが発生していそうな箇所

困ったときは、SourceTree で履歴を見ながら状況を整理するのがおすすめです。