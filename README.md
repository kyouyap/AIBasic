# Python Project Starter Set

高品質なPythonプロジェクトを迅速に開始するためのテンプレートです。GitHub Actions、pre-commit、ruff等による品質管理と、VSCode向けの開発環境が設定済みです。

## 特徴

- 包括的なコード品質管理
  - Ruffによる静的解析とフォーマット（全ルール有効）
  - GitHubワークフローによる自動テスト
  - pre-commitによる自動チェック（ruff, hadolint）
- Docker対応
  - devcontainer設定済み
  - hadolintによるDockerfileのリント
- VSCode統合
  - 推奨拡張機能の設定
  - エディタ設定の最適化
- 充実したテスト環境
  - pytest（カバレッジ、モック、サブテスト）
  - GitHub Actionsによる自動テスト

## セットアップ

1. 前提条件
   - VSCode + Docker（推奨）

2. 開発環境のセットアップ
   - VSCode + Docker（推奨）
     1. [Dev Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)拡張機能をインストール
     2. このリポジトリをクローン
     3. VSCodeで開き、右下の通知から「Reopen in Container」を選択
     4. 自動的に開発環境が構築されます


## 開発ワークフロー

### コード品質管理

- コードフォーマット
  ```bash
  ruff format .
  ```

- リント
  ```bash
  ruff check .
  ```

### テスト

- テストの実行
  ```bash
  pytest
  ```

- カバレッジレポートの生成
  ```bash
  pytest --cov=.
  coverage report
  ```

## プロジェクト構成

```
.
├── .clinerules                    # Cline AI設定
├── .devcontainer/                 # Docker開発環境設定
│   ├── devcontainer.json
│   └── Dockerfile
├── .github/                       # GitHub設定
│   ├── actions/                   # カスタムアクション
│   ├── workflows/                 # CI/CDワークフロー
│   └── dependabot.yml            # 依存関係自動更新
├── .pre-commit-config.yaml        # pre-commit設定
├── .vscode/                       # VSCode設定
│   ├── extensions.json           # 推奨拡張機能
│   └── settings.json            # エディタ設定
├── pyproject.toml                 # プロジェクト設定
├── ruff.toml                      # Ruff設定
└── uv.lock                        # 依存関係ロック
```

## 主要ファイルの説明

- **pyproject.toml**: プロジェクトの設定、依存関係、ビルド設定を管理
- **ruff.toml**: Pythonコードの静的解析とフォーマットの設定
- **.pre-commit-config.yaml**: コミット前の自動チェック設定
- **.devcontainer/**: Docker開発環境の設定
- **.github/workflows/**: GitHub Actionsによる自動テスト・デプロイの設定
