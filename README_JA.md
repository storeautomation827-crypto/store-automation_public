# Store Automation v1.01（日本語版）

アルバイト先の飲食店で使っている Excel 管理をもう少し楽にしたくて作っているプロジェクトです。
複数種別の業務入力をブラウザから行い SQLite に保存し、
確認画面でレビューした後に既存の Excel ファイルへ反映できる仕組みを作っています。

## 作った背景

アルバイト先では、日々の業務データを Excel で管理しています。
入力作業自体はシンプルですが、同じことを毎日繰り返すうちに「もう少し楽にできないか」と思い始めました。

既存の Excel ファイルをそのまま維持することが前提だったので、
「Excel を置き換える」のではなく「Web フォームから入力して Excel へ反映する補助ツール」として設計しています。

前作では LINE Bot と OCR を使ってレシート画像から情報を読み取る仕組みを作りましたが、
入力項目が増えるにつれてメッセージのやり取りが多くなってしまいました。
そこで Flask ベースの Web アプリとして作り直したのが本プロジェクトです。

→ 前作: [store-automation-ocr](https://github.com/storeautomation827-crypto/store-automation-ocr)

## システム構成

```
[ブラウザ / スマートフォン]
          ↓  HTTP
 [Flask アプリ (routes.py)]
          ↓
 [サービス層 (db.py / write_service.py)]
          ↓                       ↓
    [SQLite DB]           [Excel 操作層]
  (input_history)    backup.py / path_guard.py / writer.py
                                  ↓
                          [Excel ファイル]
```

Flask と SQLite だけで動く構成なので、店舗 PC に Python と必要なパッケージを入れれば動きます。

## 主な機能

### 入力フォーム

複数種別の業務フォームをブラウザから入力できます。
各フォームは入力種別ごとにバリデーション（整数・テキスト・必須チェックなど）を行ってから保存します。
入力種別はシステム設定で管理しており、このリポジトリでは種別名や項目の詳細は公開していません。

### データ管理
- 入力内容は SQLite の `input_history` テーブルへ `pending`（未反映）として保存
- `/history` で入力履歴を一覧確認
- `/review` で未反映データと書き込みプレビュー（Dry-Run）を確認

### Excel 反映フロー
1. `GET /review/plan` で書き込み予定を JSON で確認（Excel には一切触れない）
2. `POST /review/write_test` でテスト用 Excel コピーへ試し書き込み
3. 結果を画面で確認（書き込み件数・スキップ件数・バックアップパス）
4. 問題なければ本番 Excel へ反映し、`status` が `reflected` に更新

### 認証・権限管理
- セッションベースのログイン認証
- 複数の権限ロールを設定可能（ロール名・権限詳細は非公開）
- ロールに応じて利用できる機能が変わる構成

### 安全設計

Excel の書き込みには意図的にいくつかの制限を入れました。

**PathGuard（書き込み先の検証）**
書き込みを許可するディレクトリ（`allowed_root`）を明示的に指定し、
それ以外のパスはすべてエラーで弾きます。
`Path.resolve()` で正規化してから判定するので、`../` などを使った迂回も防ぎます。
バックアップファイル（`_backup_YYYYMMDD_HHMMSS`）や Excel 一時ファイル（`~$`）への書き込みも拒否します。

**バックアップの強制作成**
書き込み前に必ずコピーを作成します。
コピーに失敗した場合は書き込み自体を中止します。
命名規則: `元ファイル名_backup_YYYYMMDD_HHMMSS.xlsx`

**テストコピー先行**
本番 Excel ではなく、事前に準備したコピーに書き込んで結果を確認してから、
本番に反映するようにしています。

## データベース設計

テーブルは1つだけです。

```sql
CREATE TABLE input_history (
    id            INTEGER PRIMARY KEY AUTOINCREMENT,
    store_id      TEXT    NOT NULL,
    business_date TEXT    NOT NULL,        -- YYYY-MM-DD
    input_type    TEXT    NOT NULL,        -- 入力種別（設定で管理）
    data_json     TEXT,                   -- 入力内容（JSON）
    created_by    TEXT,
    created_at    TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    status        TEXT    DEFAULT 'pending'  -- pending / reflected
);
```

入力種別によって `data_json` の構造が変わりますが、テーブルは1つにまとめました。
シンプルにした分、バリデーションは Python 側（`db.py`）で丁寧に書いています。

## 処理の流れ

```
1. スタッフがブラウザでログイン
2. 入力フォームで業務データを入力
3. バリデーション後に SQLite へ pending として保存
4. /review で未反映データと書き込みプレビューを確認
5. テスト用 Excel コピーへ試し書き込み (/review/write_test)
6. 結果画面で書き込み内容・スキップ件数・バックアップパスを確認
7. 問題なければ本番 Excel へ反映、status が reflected に更新される
```

## ディレクトリ構成

```
store_automation_v1_01/
├── app/
│   ├── auth/
│   │   └── login.py            # ログイン・パスワード検証・セッション管理
│   ├── excel/
│   │   ├── backup.py           # バックアップ作成（コピーのみ・元ファイルは変更しない）
│   │   ├── path_guard.py       # 書き込みパスのホワイトリスト検証
│   │   ├── writer.py           # openpyxl によるセルへの書き込み
│   │   ├── write_plan.py       # 書き込み計画の生成・サマリー・レビュー用整形
│   │   ├── scanner.py          # Excel ファイルのスキャン
│   │   └── mappings.py         # セルマッピング設定の読み込み
│   ├── services/
│   │   ├── db.py               # SQLite 操作・バリデーション・プレビュー整形
│   │   ├── write_service.py    # テスト書き込み処理
│   │   └── pending_to_write_records.py
│   └── web/
│       └── routes.py           # Flask Blueprint ルーティング（全ルート定義）
├── config/
│   ├── stores.json.example     # 店舗設定のサンプル
│   └── users.json.example      # ユーザー設定のサンプル
├── templates/                  # HTML テンプレート
├── static/                     # CSS / JavaScript
├── tests/                      # テストコード（21ファイル）
├── scripts/                    # 開発・検証用スクリプト
├── run.py                      # Flask 起動エントリーポイント
└── requirements.txt
```

## 技術スタック

| 分類 | 使用技術 |
|---|---|
| バックエンド | Python 3.12 / Flask 3.0 |
| データベース | SQLite3（Python 標準ライブラリ） |
| フロントエンド | HTML / CSS / JavaScript |
| Excel 操作 | openpyxl 3.1 |
| テスト | unittest / pytest |

`requirements.txt` に含まれるのは Flask・Werkzeug・openpyxl の3つだけです。

## セットアップ

```bash
git clone https://github.com/storeautomation827-crypto/store-automation_public.git
cd store-automation_public

python -m venv venv
venv\Scripts\activate    # Windows
# source venv/bin/activate  # macOS / Linux

pip install -r requirements.txt

copy config\stores.json.example config\stores.json
copy config\users.json.example config\users.json

python run.py
```

## テスト

テストファイルは21個あります。
DB操作・Excel書き込み・パスガード・ルーティングなど処理ごとに分けています。

```bash
pytest tests/
```

テストはすべて一時ディレクトリ上の SQLite を使うか、
openpyxl の読み込み自体を行わない設計にして、
本番 DB や本番 Excel ファイルに影響が出ないようにしています。

## 今後やりたいこと

- 本番 Excel への反映ボタンを有効化（現在はテスト用コピーへの書き込みまで対応）
- スマホからの操作性をさらに改善
- 複数店舗への対応
- 管理者向けのログ閲覧・操作確認画面の充実
- Windows 向けランチャーアプリの整備

## 公開範囲について

このリポジトリには Flask アプリのコア部分・SQLite 操作・Excel 連携処理・テストコードを掲載しています。
実店舗名・実際の業務データ・本番の Excel ファイル・入力種別の詳細・店舗固有の設定ファイルは含みません。

## ライセンス

ポートフォリオ・学習用途での公開です。
