# 📘 The Move Book 日本語翻訳サイト

― 概要・技術構成・運用方針まとめ（ドキュメント版） ―

## 1. サイト概要

### 1-1. 目的

本サイトは、Mysten Labs が公開する “The Move Book” および “Move Language Reference” を日本語で読めるようにすることを目的とした **非公式の翻訳サイト** です。

Move 言語は Sui ブロックチェーンや Aptos で用いられるプログラミング言語であり、学習者が正しく安全に Move を理解できるよう、日本語での読みやすいドキュメントを提供します。

### 1-2. 翻訳方針

- 原文（English）の内容をできるだけ忠実に翻訳する
- 技術用語や Move 固有の概念は文脈に応じて **統一された日本語表現** を使う
- 誤解を避けるため、必要に応じて原文の併記を行う
- 原文の更新に追随し、翻訳との差分を定期的に反映する

### 1-3. 翻訳の対象範囲

- `/book`: Move Book（チュートリアル）
- `/reference`: Move Language Reference
- `/packages` に含まれるコード例の説明
- サイト内のナビゲーション・メニュー・目次など

> Move Book のライセンスは Apache 2.0 のため、翻訳・再公開は許可されています（原文のクレジット表記は必須）。

### 1-4. 翻訳サイトの公開先

- GitHub Pages（`gh-pages` ブランチ）
- カスタムドメインまたは `username.github.io/move-book-ja/` 形式

---

## 2. 技術構成

### 2-1. ベースとなるリポジトリ

- ベース: `MystenLabs/move-book`
- 翻訳用にフォークした独自リポジトリ: 例）`yourname/move-book-ja`

フォーク後、翻訳サイト向けに以下を変更します。

| 対象 | 内容 |
| --- | --- |
| `site/docusaurus.config.ts` | サイト名、URL、`baseUrl` の変更 |
| `CNAME` | 原文 `move-book.com` を削除、または自前のドメインに変更 |
| `.github/workflows/` | GitHub Pages デプロイ設定を自分用に調整 |

### 2-2. フロントエンド構成（Docusaurus）

翻訳サイトは **Docusaurus + React + TypeScript** で構築します。

主要モジュールの役割は以下の通りです。

| モジュール | 役割 |
| --- | --- |
| Docusaurus | 静的ドキュメントサイト生成 |
| MDX | Markdown + React コンポーネント |
| pnpm | パッケージ管理 |
| GitHub Actions | CI/CD、ビルド & Pages デプロイ |

開発・ビルド用コマンド例:

```bash
pnpm install
pnpm start   # ローカル開発
pnpm build   # 本番ビルド
pnpm serve   # build ディレクトリをローカル公開
```

### 2-3. ディレクトリ構成（翻訳版）

翻訳サイト向けの基本構成は以下の通りです。

```
move-book-ja/
├── book/                # Move Book (日本語翻訳)
├── reference/           # Move Language Reference (日本語翻訳)
├── site/                # Docusaurus プロジェクト
│   ├── docusaurus.config.ts
│   ├── src/
│   └── static/
├── packages/            # コード例
├── CNAME                # optional（独自ドメインがある場合）
├── README.md            # このドキュメント
└── .github/
    ├── workflows/
    │   └── deploy.yml   # GitHub Pages 用 CI
```

翻訳方式は、最もシンプルな **「原文の Markdown を直接日本語に置き換える」** 方式を採用します。

- ✅ シンプル
- ✅ Docusaurus の i18n 設定不要
- ✅ 初期構築が早い

（将来的に `/ja/` を追加する拡張にも対応可能）

### 2-4. GitHub Pages デプロイ

翻訳リポジトリでは GitHub Actions で以下を行うワークフローを設定します。

1. `main` ブランチに push → CI 起動
2. `pnpm build` を実行
3. 出力された `build/` ディレクトリを `gh-pages` ブランチへ force push
4. GitHub Pages が `gh-pages` を公開

`deploy.yml`（サンプル）

```yaml
name: Deploy Docusaurus to GitHub Pages

on:
  push:
    branches: [ "main" ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout source
        uses: actions/checkout@v3

      - name: Setup Node
        uses: actions/setup-node@v3
        with:
          node-version: 18

      - name: Install pnpm
        run: npm install -g pnpm

      - name: Install dependencies
        run: pnpm install

      - name: Build website
        run: pnpm build

      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./build
          publish_branch: gh-pages
```

### 2-5. GitHub Pages デプロイ手順（ステップバイステップ）

1. **リポジトリの準備**
   - `MystenLabs/move-book` をフォークし、ローカルに clone します。
   - `site/docusaurus.config.ts` の `url` を `https://<your-account>.github.io`、`baseUrl` を `/move-book-ja/` など公開したいサブパスに合わせて更新します。
   - `organizationName` と `projectName` を自分の GitHub アカウントとリポジトリ名に変更します。

2. **依存関係のインストールとビルド確認**
   ```bash
   cd site
   pnpm install
   pnpm build
   pnpm serve # http://localhost:3000 で表示を確認
   ```

3. **GitHub Actions（`.github/workflows/main.yml`）の確認**
   - `pnpm/action-setup` と `peaceiris/actions-gh-pages` が設定されていることを確認します。
   - `publish_dir: ./site/build` のように Docusaurus のビルド出力パスが正しく指定されているか確認します。

4. **コミット＆ push**
   ```bash
   git add site/docusaurus.config.ts .github/workflows/main.yml
   git commit -m "Configure GitHub Pages deployment"
   git push origin main
   ```

5. **GitHub Pages の有効化**
   - GitHub のリポジトリ設定 → **Pages** で、Source を `Deploy from a branch`、Branch を `gh-pages` に設定します。
   - GitHub Actions のワークフローが走り、`gh-pages` ブランチが自動で作成されます。

6. **公開確認**
   - Actions のデプロイが成功したら `https://<your-account>.github.io/move-book-ja/` にアクセスして表示を確認します。
   - サイトが 404 になる場合は `baseUrl` や GitHub Pages のブランチ設定を再度確認します。

---

## 3. 運用方針

### 3-1. 翻訳プロセス

1. 原文との差分を確認
   - 上流（`MystenLabs/move-book`）の更新を `upstream` として追加
   - 定期的に `git fetch upstream` → `git merge` または `rebase`
   - マージ時に発生した差分は該当ファイルだけをレビューし、必要な箇所のみ再翻訳する（`CNAME` など削除したファイルは自分のブランチで削除済みにしておけば復活しない）
2. 翻訳作業
   - 小さな単位で PR を分ける（章ごとなど）
   - 技術用語はプロジェクト内で **統一用語リスト** を作って管理
3. 原文併記の判断
   - Move 特有の語句の訳が曖昧なとき
   - 安全性や型システム関連で誤訳が致命的になるところ
   - エラーメッセージやコードブロック

### 3-2. 更新頻度

- **週 1 回** を目安に upstream（`MystenLabs/move-book`）から原文を取り込み、差分を確認・再翻訳する
- プルリク作成 → レビュー → 翻訳反映
- 必要に応じて補足説明・脚注を追加

### 3-3. ライセンスとクレジット

Move Book は Apache 2.0 ライセンスのため、以下が可能です。

- 翻訳を公開してよい
- 商用利用も可能
- ただし原著のクレジット表記は必須

本サイトで明記すべき項目（必須）

- 「Original: The Move Book by Mysten Labs」
- 「Licensed under Apache 2.0」
- 翻訳チーム名または責任者名（任意）

### 3-4. 品質管理

- 翻訳ガイドライン（統一用語リスト / 文体ルール / 校正ルール）をリポジトリ内の `CONTRIBUTING.md` にまとめる
- PR 時の自動 lint（Markdown Lint / Spellcheck）の導入も推奨

---

## 📌 付録：翻訳サイトの運用ロードマップ

1. フォーク → リポジトリ準備
2. Docusaurus 設定変更
3. GitHub Pages デプロイ確認
4. 翻訳方針・統一用語リストの作成
5. 翻訳作業スタート
6. 翻訳サイト正式公開
7. 原著との更新同期開始
