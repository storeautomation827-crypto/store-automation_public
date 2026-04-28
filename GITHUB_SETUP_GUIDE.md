# GitHub セットアップガイド

このガイドでは、store-automation を GitHub に公開するための手順を説明します。

## 1. GitHub リポジトリの作成

### ステップ 1: GitHub にアクセス
1. [github.com](https://github.com) にアクセスしてログイン
2. storeautomation827 アカウントでログインしていることを確認

### ステップ 2: 新規リポジトリを作成
1. 右上の **+** アイコン → **New repository** をクリック
2. リポジトリ設定：
   - **Repository name**: `store-automation`
   - **Description**: `Flask-based store operations automation platform`
   - **Public**: チェック（公開リポジトリにする）
   - **Initialize this repository with**: チェックなし（ローカルで git init するため）
3. **Create repository** をクリック

### ステップ 3: リポジトリ URL をコピー
- HTTPS か SSH のいずれかを選択
- URL をコピー：例）`https://github.com/storeautomation827/store-automation.git`

---

## 2. ローカル フォルダの準備

### ステップ 1: 公開用フォルダを作成
```bash
# 新しいフォルダを作成
mkdir store-automation-github
cd store-automation-github
```

### ステップ 2: ファイル構成を準備
`C:\Users\rock1\Desktop\store_automation_v1_01` から以下をコピー：

**コピー対象（公開ファイル）:**
```
app/                    ← すべて
templates/              ← すべて
static/                 ← すべて
tests/                  ← すべて
scripts/                ← すべて
docs/                   ← すべて
requirements.txt        ← そのまま
run.py                  ← そのまま
```

**作成するファイル:**
- README.md（このリポジトリに含まれます）
- .gitignore（このリポジトリに含まれます）
- LICENSE（任意）
- config/stores.json.example（このリポジトリに含まれます）
- config/users.json.example（このリポジトリに含まれます）

**コピー不要（除外）:**
- config/.secret_key
- config/users.json
- config/stores.json
- data/（データベースファイル）
- excel_files/（実際のデータ）
- __pycache__/
- .pytest_cache/
- .claude/
- CLAUDE.md、AGENTS.md
- .env ファイル

---

## 3. Git リポジトリの初期化と設定

### ステップ 1: Git ユーザー設定（初回のみ）
```bash
git config --global user.name "はると"
git config --global user.email "storeautomation827@gmail.com"
```

### ステップ 2: git init と remote 設定
```bash
# リポジトリを初期化
git init

# リモートリポジトリを追加（下記は例です。実際のURLを使用）
git remote add origin https://github.com/storeautomation827/store-automation.git

# main ブランチに切り替え（古いGitの場合は master）
git branch -M main
```

### ステップ 3: ファイルをステージング
```bash
# すべてのファイルをステージング
git add .

# ステージング状況を確認（除外すべきファイルがないか確認）
git status
```

### ステップ 4: 最初のコミット
```bash
git commit -m "Initial commit: Core automation infrastructure for job application showcase"
```

---

## 4. GitHub への Push

### ステップ 1: 認証設定

#### 方法 A: GitHub CLI（推奨）
```bash
gh auth login
# → GitHub.com を選択
# → HTTPS を選択
# → "Y" で認証ブラウザを開く
# → ブラウザでスコープを承認
```

#### 方法 B: Personal Access Token (PAT)
1. GitHub → Settings → Developer settings → Personal access tokens → Generate new token
2. スコープ: `repo`（フルコントロール）を選択
3. トークンをコピーして安全に保管

```bash
# Push 時にトークンを使用
git push -u origin main
# → Username: storeautomation827
# → Password: [your personal access token]
```

### ステップ 2: Push 実行
```bash
git push -u origin main
```

---

## 5. GitHub リポジトリの設定

### ステップ 1: リポジトリ設定を確認
1. GitHub → リポジトリ → **Settings**
2. **General** で公開設定を確認

### ステップ 2: Topics を追加（SEO 最適化）
- **Repository topics**: `python`, `flask`, `automation`, `business-automation`, `portfolio`

### ステップ 3: About セクションを編集
- **Description**: Flask-based store operations automation platform
- **Website**: ポートフォリオサイト（あれば）

---

## 6. トラブルシューティング

### Git コマンドが見つからない
```bash
# Git がインストール済みか確認
git --version

# インストールされていない場合：Git をインストール
# https://git-scm.com/download/win
```

### 認証エラー
```bash
# キャッシュされた認証情報をリセット
git credential-manager erase https://github.com

# その後、再度 push を実行
git push -u origin main
```

### .gitignore が機能していない
```bash
# キャッシュをリセット
git rm -r --cached .
git add .
git commit -m "Fix gitignore"
```

---

## 7. 今後の更新

コードを更新した場合：
```bash
# 変更をステージング
git add .

# コミット
git commit -m "feat: description of changes"

# Push
git push origin main
```

---

## チェックリスト

- [ ] GitHub アカウント（storeautomation827）でログイン
- [ ] 新規リポジトリ `store-automation` を作成
- [ ] 公開対象ファイルをローカルにコピー
- [ ] .gitignore と README.md を配置
- [ ] `git init` → `git remote add origin` を実行
- [ ] `git add .` → `git commit` → `git push` を実行
- [ ] GitHub で リポジトリが正しく表示されることを確認
- [ ] Topics を追加して検索最適化

---

**質問や問題が発生した場合は、GitHub Issues で質問できます。**
