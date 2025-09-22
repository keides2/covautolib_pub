# covautolib（公開用サニタイズ版）

本リポジトリは、社内用の Coverity® Connect 自動化ツール「covautolib」を外部公開向けに再構成したものです。Coverity REST API (v2) をラップして、プロジェクト情報の取得、スナップショット差分確認、ユーザー管理、CSV/JSON エクスポートなどを支援します。社内固有のホスト・認証情報はすべて環境変数で設定できるように差し替えています。

## 主な機能
- Coverity REST API を用いたプロジェクト／ストリーム／スナップショット／Issue／ユーザー情報の取得。
- 取得した情報を `pandas` や `openpyxl` を利用して CSV／JSON／Excel に出力。
- SMTP 経由でレポートメールを送信（接続先は環境変数で設定可能）。
- GitLab・Coverity のアカウント管理支援スクリプトを同梱。

## 必要環境
- Python 3.9 以上（Windows / Linux の CPython でテスト済み）。
- Python パッケージ：`requests`、`urllib3`、`openpyxl`、`pandas`
  - 例: `pip install requests urllib3 openpyxl pandas`

## 環境変数の設定
Coverity 認証情報・プロキシ・SMTP などは環境変数から読み込みます。`covautolib_pub/.env.example` を `.env` にコピーして値を設定してください。

```powershell
# Windows PowerShell の例
Copy-Item .env.example .env
notepad .env  # 値を編集

# 一時的に環境変数を読み込む
Get-Content .env |
  Where-Object { $_ -and $_ -notmatch '^#' } |
  ForEach-Object {
    $name, $value = $_ -split '=', 2
    [System.Environment]::SetEnvironmentVariable($name.Trim(), $value.Trim())
  }
```

### 主な環境変数一覧
| 変数名 | 用途 |
| --- | --- |
| `COVAUTHUSER`, `COVAUTHKEY` | Coverity API の Basic 認証ユーザー名／キー |
| `COVAUTO_API_BASE_URL` | Coverity API ベース URL（例: `https://coverity.example.com/api/v2`）|
| `COVAUTO_HTTP_PROXY`, `COVAUTO_HTTPS_PROXY` | 既定のプロキシ設定（任意）|
| `COVAUTO_ALT_HTTP_PROXY`, `COVAUTO_ALT_HTTPS_PROXY` | 代替プロキシ設定（任意）|
| `COVAUTO_RUNTIME_HTTP_PROXY`, `COVAUTO_RUNTIME_HTTPS_PROXY` | 実行時に上書きしたいプロキシ（任意）|
| `COVAUTO_SMTP_HOST`, `COVAUTO_SMTP_PORT` | `GLMail` が利用する SMTP サーバー（既定: `localhost:25`）|
| `COVAUTO_BASE_DIR` | 作業ディレクトリのルート（既定: `~/cov`）|
| `COVAUTO_SHARE_DIR` | 共有ディレクトリ（既定: `COVAUTO_BASE_DIR` と同一）|

上記以外にも、必要に応じて独自の環境変数を追加し、コードから参照することができます。

## 利用方法
1. 依存パッケージをインストールします。例: `pip install requests urllib3 openpyxl pandas`
2. `.env` を準備し、必要な環境変数を設定します（または OS 側で設定）。
3. 対象スクリプトを実行します。例:
   ```powershell
   python covautolib_3.py --help
   ```
   `covautolib_3.py` ではクラス単位の機能が多数実装されています。必要に応じて自作スクリプトからインポートして利用してください。

## 開発に関するメモ
- `covautolib_3.py` は歴史的経緯から単一ファイルで機能が集中しています。機能拡張時にはモジュール化を進めると保守性が向上します。
- 機密情報を埋め込まないために、定数値は環境変数や外部設定ファイルから注入する方針を維持してください。
- 実行結果として生成される `*.csv`、`*.json`、`*.log`、`__pycache__` などは不要になったら削除しましょう。

## ライセンス
同梱の `LICENSE` に従います。再配布・改変時は内容をご確認ください。

## 英語版 README
詳細な英語版 README は `README.md` を参照してください。
