# 【初心者向け】GitHub & Sourcetree SSH接続設定ガイド

このガイドでは、GitHubと安全にやり取りするための「SSH接続」の設定方法を、MacとWindowsそれぞれの環境に合わせて解説します。
コマンド入力が必要な箇所は、コピー＆ペーストで実行できるように記載しています。

---

## 前提: SSH接続とは？

「SSH鍵（かぎ）」という電子的な鍵ペア（秘密鍵と公開鍵）を作って認証する方法です。
- **秘密鍵**: あなたのパソコンの中に大切にしまっておく鍵。
- **公開鍵**: GitHubに渡して登録しておく鍵。

この2つが揃うことで、「これは確かにあなたのパソコンからのアクセスだ」と証明されます。

---

## 手順1: SSH鍵を作成する

お使いのOSに合わせて操作してください。

### 🍎 Macの方 (ターミナルを使用)

1. **「ターミナル」アプリ** を開きます（`Cmd + Space` で「terminal」と検索すると早いです）。
2. 以下のコマンドをコピーして、ターミナルに貼り付け、Enterキーを押します。
   ```bash
   mkdir -p ~/.ssh && chmod 700 ~/.ssh
   ```
3. 続けて、鍵を作成する以下のコマンドを実行します。
   Replace `your_email@example.com` with your actual email address.
   ※ 途中で「パスフレーズ」を聞かれますが、何も入力せず Enter を2回押してOKです（簡略化のため）。
   ```bash
   ssh-keygen -t ed25519 -C "your_email@example.com" -f ~/.ssh/id_ed25519
   ```
4. GitHub用の設定ファイルを作成して開きます。
   ```bash
   touch ~/.ssh/config
   open -e ~/.ssh/config
   ```
   テキストエディタが開くので、以下の内容を貼り付けて保存し、閉じてください。
   ```text
   Host github.com
     HostName github.com
     User git
     IdentityFile ~/.ssh/id_ed25519
     AddKeysToAgent yes
     UseKeychain yes
   ```
5. 最後に、作成した鍵をシステムに登録します。
   ```bash
   ssh-add --apple-use-keychain ~/.ssh/id_ed25519
   ```

### 🪟 Windowsの方 (PowerShellを使用)

1. **「PowerShell」** を開きます（スタートメニューで「powershell」と検索）。
2. 以下のコマンドをコピーして貼り付け、Enterキーを押して鍵を作成します。
   ※ `your_email@example.com` はご自身のメールアドレスに書き換えてください。
   ※ 途中で何か聞かれても、すべて Enter キーを押して進めて構いません。
   ```powershell
   ssh-keygen -t ed25519 -C "your_email@example.com" -f $env:USERPROFILE\.ssh\id_ed25519
   ```
3. 設定ファイルを作成します。以下のコマンドでメモ帳が開きます。
   ```powershell
   notepad $env:USERPROFILE\.ssh\config
   ```
   メモ帳が開いたら、以下の内容を貼り付けて保存し、閉じてください。
   ```text
   Host github.com
     HostName github.com
     User git
     IdentityFile ~/.ssh/id_ed25519
   ```
   ※ Windowsの場合、IdentityFileのパスは `~` で認識されることが多いですが、うまくいかない場合は絶対パス（`C:\Users\ユーザー名\.ssh\id_ed25519`）を指定してください。

---

## 手順2: 公開鍵をコピーしてGitHubに登録する

作成した「公開鍵」の中身をクリップボードにコピーします。

- **Macの場合**:
  ```bash
  pbcopy < ~/.ssh/id_ed25519.pub
  ```
- **Windowsの場合 (PowerShell)**:
  ```powershell
  Get-Content $env:USERPROFILE\.ssh\id_ed25519.pub | Set-Clipboard
  ```

コピーできたら、GitHubに登録します。

1. [GitHubのSSH設定ページ](https://github.com/settings/ssh/new) (Settings > SSH and GPG keys > New SSH key) を開きます。
2. **Title** には「MacBook Air」や「Windows PC」など、どのパソコンかわかる名前を付けます。
3. **Key** 欄で「貼り付け（ペースト）」を行います (`Cmd+V` / `Ctrl+V`)。`ssh-ed25519` から始まる文字列が入ればOKです。
4. **Add SSH key** ボタンをクリックして完了です。

---

## 手順3: Sourcetreeを設定する

1. **Sourcetree** を起動し、設定画面を開きます。
   - Mac: メニューバー「Sourcetree」>「設定 (Preferences)」>「アカウント」
   - Windows: ツール > オプション > 一般
2. アカウント設定（または認証設定）で、**SSH鍵** を指定する場所を探します。
3. 作成した鍵ファイルを選択します。
   - Macの場所: `/Users/ユーザー名/.ssh/id_ed25519` 
     (隠しフォルダにあるため、選択画面で `Cmd + Shift + .` を押すと表示されます)
   - Windowsの場所: `C:\Users\ユーザー名\.ssh\id_ed25519`
4. **重要**: Sourcetreeの設定で「SSHクライアント」を選ぶ項目がある場合、**OpenSSH** を選択してください。（PuTTY/PlinkではなくOpenSSHの方がトラブルが少ないです）

### ⚠️ トラブルシューティング

もしSourcetreeで「キーが見つかりません」と言われたり、Gitコマンドが動かない場合：

1. **Sourcetreeを再起動** してみてください。設定変更後は再起動しないと反映されないことがあります。
2. Windowsの方は、Sourcetreeの「ツール > オプション > 一般 > SSHクライアントの設定」が **OpenSSH** になっているか確認してください。
3. それでもダメな場合、`~/.ssh/config` ファイルの記述ミスがないか確認してください。

---

以上で設定は完了です！
これで、パスワードを入力することなく安全にGitHubと連携できるようになります。
