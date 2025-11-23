# YOURLS Docker 日本語版

YOURLSとMySQL 8.0をDocker Composeで簡単に構築できる環境です。日本語化ファイル（YOURLS-ja_JP）を組み込んでいます。

## 概要

このプロジェクトは、[YOURLS](https://yourls.org/)（Your Own URL Shortener）をDockerコンテナで簡単に実行できるようにしたものです。以下の特徴があります：

- **Docker Compose**: YOURLSとMySQL 8.0を1つの設定ファイルで管理
- **日本語対応**: YOURLS-ja_JPによる完全な日本語化
- **簡単セットアップ**: 環境変数による柔軟な設定
- **データ永続化**: ボリュームマウントによるデータ保護

## ライセンス

このプロジェクトはMITライセンスの下で公開されています。

- **YOURLS**: MIT License
- **YOURLS-ja_JP**: MIT License
- **このプロジェクト**: MIT License（詳細は[LICENSE.txt](LICENSE.txt)を参照）

## 必要要件

- Docker
- Docker Compose
- Git

## インストール

### 1. リポジトリのクローン

```bash
git clone https://github.com/potofo/yourls
cd yourls
```

### 2. 環境変数の設定

`.env.example`をコピーして`.env`ファイルを作成し、必要に応じて設定を変更します。

```bash
cp .env.example .env
```

`.env`ファイルの主要な設定項目：

```bash
# YOURLSのサイトURL（外部アクセス用のURL）
YOURLS_SITE=http://localhost

# YOURLS管理者のユーザー名とパスワード
YOURLS_USER=admin
YOURLS_PASS=admin

# 言語設定（ja_JP: 日本語）
YOURLS_LANG=ja_JP

# タイムゾーンオフセット（日本標準時は9）
YOURLS_HOURS_OFFSET=9

# 外部アクセス用のポート（デフォルト: 80）
YOURLS_EXTERNAL_PORT=80

# MySQL設定
MYSQL_ROOT_PASSWORD=example
MYSQL_DATABASE=yourls
MYSQL_USER=yourls
MYSQL_PASSWORD=yourls
```

**セキュリティのため、本番環境では必ずパスワードを変更してください。**

### 3. Dockerコンテナの起動

```bash
docker compose up -d
```

初回起動時は、イメージのダウンロードとデータベースの初期化に時間がかかる場合があります。

### 4. YOURLSのセットアップ

ブラウザで `http://localhost/admin/` にアクセスし、初期セットアップを完了します。

1. 「Install YOURLS」ボタンをクリック
2. データベーステーブルが自動的に作成されます
3. `.env`で設定したユーザー名とパスワードでログイン

## 使用方法

### コンテナの起動

```bash
docker compose up -d
```

### コンテナの停止

```bash
docker compose down
```

### コンテナのログ確認

```bash
# 全コンテナのログ
docker compose logs -f

# YOURLSコンテナのみ
docker compose logs -f yourls

# MySQLコンテナのみ
docker compose logs -f db
```

### コンテナの再起動

```bash
docker compose restart
```

## データベースの初期化

データベースを完全に削除して初期状態に戻す場合は、以下の手順を実行します。

### ⚠️ 警告

この操作を実行すると、**全ての短縮URLとデータが完全に削除**されます。実行前に必ずバックアップを取ってください。

### 初期化手順

1. **コンテナの停止とボリュームの削除**

```bash
docker compose down --volumes
```

2. **MySQLデータディレクトリの削除**

```bash
# Windowsの場合（PowerShell）
Remove-Item -Recurse -Force .\volumes\mysql

# macOS/Linuxの場合
rm -rf ./volumes/mysql
```

3. **コンテナの再起動**

```bash
docker compose up -d
```

4. **YOURLSの再セットアップ**

ブラウザで `http://localhost/admin/` にアクセスし、再度初期セットアップを実行します。

## プロジェクト構成

```
.
├── config/
│   ├── config.php              # YOURLS設定ファイル
│   ├── config-sample.php       # 設定ファイルのサンプル
│   ├── languages/
│   │   └── ja_JP.mo           # 日本語翻訳ファイル
│   ├── plugins/               # プラグインディレクトリ
│   └── pages/                 # カスタムページ
├── volumes/
│   └── mysql/                 # MySQLデータ（自動生成）
├── .env                       # 環境変数設定（git管理外）
├── .env.example              # 環境変数のサンプル
├── docker-compose.yml        # Docker Compose設定
├── LICENSE.txt               # ライセンスファイル
└── README.md                 # このファイル
```

## プラグインの有効化

プラグインを有効化するには、YOURLS管理画面から以下の手順で行います：

1. ブラウザで`http://localhost/admin/`にアクセスしてログイン
2. 上部メニューから「機能拡張を管理」をクリック
3. 有効化したいプラグインの「有効」ボタンをクリック

利用可能なサンプルプラグイン：
- `random-shorturls` - ランダムな短縮URLを生成
- `hyphens-in-urls` - URLにハイフンを使用可能に
- `random-bg` - ランダムな背景パターン
- `sample-toolbar` - カスタムツールバーのサンプル

## トラブルシューティング

### ポート80が使用中

ポート80が既に使用されている場合、`.env`ファイルの`YOURLS_EXTERNAL_PORT`を変更してください。

```bash
YOURLS_EXTERNAL_PORT=8080
```

変更後、`http://localhost:8080/admin/` でアクセスしてください。

### データベース接続エラー

データベースコンテナの起動を確認してください：

```bash
docker compose ps
```

全てのコンテナが`Up`状態であることを確認します。

### 日本語が表示されない

1. `.env`ファイルで`YOURLS_LANG=ja_JP`が設定されていることを確認
2. `config/languages/ja_JP.mo`が存在することを確認
3. コンテナを再起動: `docker compose restart`

## バックアップ

### データベースのバックアップ

```bash
docker compose exec db mysqldump -u yourls -pyourls yourls > backup.sql
```

### データベースの復元

```bash
docker compose exec -T db mysql -u yourls -pyourls yourls < backup.sql
```

## 参考リンク

- [YOURLS公式サイト](https://yourls.org/)
- [YOURLS GitHub](https://github.com/YOURLS/YOURLS)
- [YOURLS-ja_JP](https://github.com/havill/YOURLS-ja_JP)
- [Docker公式ドキュメント](https://docs.docker.com/)

## コントリビューション

バグ報告や機能提案は、GitHubのIssuesでお願いします。

## ライセンス

MIT License - 詳細は[LICENSE.txt](LICENSE.txt)を参照してください。