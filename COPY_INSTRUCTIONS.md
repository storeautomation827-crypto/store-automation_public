# ファイルコピー手順

このフォルダ（`store-automation-public`）は、GitHub に公開するための準備フォルダです。

以下の手順で、元のプロジェクトから公開対象のファイルをコピーしてください。

---

## ステップ 1: ファイルのコピー

元のプロジェクト: `C:\Users\rock1\Desktop\store_automation_v1_01`  
公開フォルダ: `C:\Users\rock1\Desktop\store-automation-public`

### コピー対象フォルダ（すべてのファイルをコピー）

以下を **コピー元フォルダ** から **公開フォルダ** へコピーしてください：

```
✓ app/              → 完全にコピー
✓ templates/        → 完全にコピー
✓ static/           → 完全にコピー
✓ tests/            → 完全にコピー
✓ scripts/          → 完全にコピー
✓ docs/             → 完全にコピー
✓ requirements.txt  → コピー
✓ run.py            → コピー
```

### 除外すべきフォルダ（コピーしない）

```
✗ config/.secret_key
✗ config/users.json
✗ config/stores.json
✗ data/             （データベースファイル）
✗ excel_files/      （実際の店舗データ）
✗ __pycache__/
✗ .pytest_cache/
✗ .claude/
✗ .env              （ローカル環境設定）
✗ CLAUDE.md
✗ AGENTS.md
```

---

## ステップ 2: フォルダ構成を確認

コピー後、`store-automation-public` フォルダは以下のような構成になります：

```
store-automation-public/
├── README.md                    ✓ 既にあります
├── .gitignore                   ✓ 既にあります
├── GITHUB_SETUP_GUIDE.md        ✓ 既にあります
├── COPY_INSTRUCTIONS.md         ✓ このファイル
├── config/
│   ├── .gitkeep                 ✓ 既にあります
│   ├── stores.json.example      ✓ 既にあります
│   └── users.json.example       ✓ 既にあります
├── app/                         ← コピーして下さい
├── templates/                   ← コピーして下さい
├── static/                      ← コピーして下さい
├── tests/                       ← コピーして下さい
├── scripts/                     ← コピーして下さい
├── docs/                        ← コピーして下さい
├── requirements.txt             ← コピーして下さい
└── run.py                       ← コピーして下さい
```

---

## ステップ 3: Git 初期化とセットアップ

### Windows PowerShell または Git Bash を開く

```bash
# 公開フォルダへ移動
cd C:\Users\rock1\Desktop\store-automation-public

# Git ユーザー設定（初回のみ）
git config --global user.name "はると"
git config --global user.email "storeautomation827@gmail.com"

# Git リポジトリを初期化
git init

# リモートリポジトリを追加
# ※ https://github.com/storeautomation827/store-automation.git に置き換えてください
git remote add origin https://github.com/storeautomation827/store-automation.git

# ブランチを main に設定
git branch -M main

# すべてのファイルをステージング
git add .

# ステージング状況を確認（確認してから commit）
git status

# コミット
git commit -m "Initial commit: Core automation infrastructure for job application showcase"

# GitHub へ Push（要認証）
git push -u origin main
```

---

## ステップ 4: GitHub リポジトリ作成

詳細は `GITHUB_SETUP_GUIDE.md` を参照してください。

1. GitHub で新規リポジトリを作成
2. リポジトリ名: `store-automation`
3. 説明: `Flask-based store operations automation platform`
4. **Public** に設定
5. **Initialize with README** はチェックしない

---

## ステップ 5: 認証と Push

GitHub CLI または Personal Access Token を使用して Push してください。

**方法 A: GitHub CLI（推奨）**
```bash
gh auth login
git push -u origin main
```

**方法 B: Personal Access Token**
```bash
# GitHub → Settings → Developer settings → Personal access tokens で生成
git push -u origin main
# Username: storeautomation827
# Password: [Personal Access Token]
```

---

## チェックリスト

- [ ] `app/`, `templates/`, `static/`, `tests/`, `scripts/`, `docs/` をコピー
- [ ] `requirements.txt` と `run.py` をコピー
- [ ] `store-automation-public` フォルダ構成を確認
- [ ] GitHub で新規リポジトリを作成
- [ ] `git init` → `git add .` → `git commit` を実行
- [ ] `git push -u origin main` で Push
- [ ] GitHub でリポジトリが正しく表示されることを確認

---

## トラブルシューティング

**Q: ファイルをコピーしたのに git が認識しない**  
A: ターミナルで `git add .` を実行してください

**Q: 認証エラーが出た**  
A: GitHub CLI で `gh auth login` を実行するか、Personal Access Token を生成してください

**Q: ファイルが大きすぎて Push できない**  
A: `.gitignore` で除外されていないファイルが含まれていないか確認してください

---

**すべて完了したら、GitHub リポジトリが就活用ポートフォリオとして公開されます！**
