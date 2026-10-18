# cursor-thread-tools

[English](README.en.md)

Cursor の Composer / Agent スレッドの会話を Markdown にエクスポートするツールです。Cursor の**拡張機能**としても、ターミナルから使う **CLI** としても使えます。

Cursor 標準の「Export Transcript」は GUI の保存ダイアログが必須で、コマンドパレットにも出ません。本ツールはローカルの SQLite（`state.vscdb`）を**読み取り専用**で開き、そこからトランスクリプトを再構成します。会話の読み取りに Cursor の非公開 API は使いません。

**ステータス:** 保守モードです。大きな機能追加の予定はありません。動作を確認した環境は **Cursor 3.25.x（Electron 42）** です。VS Code / Cursor の Marketplace には公開していないので、Release の `.vsix` を使うか、ソースからビルドして導入してください。

## 機能

- スレッドの一覧表示（名前、メッセージ数、Agent / Chat の区別、作成日時）
- スレッドの Markdown エクスポート（thinking ブロックを含めるかどうかは選べる）
- コマンドパレットからの出力先ディレクトリの変更
- エクスポートしたファイルを Composer に `@` 参照として自動で追加（Cursor の内部コマンドを使うため、追加できない場合はエクスポートだけを行う）
- バックグラウンドでの自動保存（既定ではオフ）
- CLI の `cursor-thread-tools list` / `export`

## クイックスタート

### 拡張機能（多くの人はこちら）

1. [GitHub Releases](https://github.com/stlwolf/cursor-thread-tools/releases) から、自分の環境に合う `.vsix` をダウンロードします。
2. Cursor で `Cmd+Shift+P` → `Extensions: Install from VSIX...` を実行し、ダウンロードしたファイルを選んでから、ウィンドウをリロードします。
3. `Cmd+Shift+P` → `Thread Tools: List Threads` または `Thread Tools: Export Thread to Markdown` を実行します。

`.vsix` にはネイティブモジュールの `better-sqlite3` が入っているため、OS とアーキテクチャが一致するファイルを使ってください。現在の Release は Apple Silicon 向け（`darwin-arm64`）だけです。

### CLI

```bash
npm install
bash scripts/setup-cli.sh   # Node.js 向けに rebuild して npm link する

cursor-thread-tools list
cursor-thread-tools export --all --since 7d
```

インストールとビルドの詳しい手順は [INSTALL.md](INSTALL.md) を、CLI のオプションと使用例は [CLI_USAGE.md](CLI_USAGE.md) を参照してください。

## 要件

- macOS（Windows / Linux のパスにも対応していますが、テストはしていません）
- CLI を使う場合とソースからビルドする場合は Node.js **20 以上**（`@electron/rebuild` を使うなら **22 以上**を推奨）
- Cursor を一度は起動していること（`state.vscdb` が作られている必要があります）
- 拡張機能のビルド対象は **Electron 42.10.0**（`ELECTRON_VERSION=...` で変更できます）

## 仕組み

1. `state.vscdb` を読み取り専用で開きます。
2. `cursorDiskKV` テーブルから `composerData:*` のメタデータを読みます。
3. `conversationState`（protobuf）をデコードし、そこから参照される `agentKv:blob` をたどってメッセージ本文を取り出します。
4. Markdown として出力します。

SQLite ライブラリの選定、protobuf の解析方法、配布方法などの設計判断は [`docs/decisions/`](docs/decisions/) にまとめています。

## ディレクトリ構成

```text
cursor-thread-tools/
├── README.md               # 本ファイル（日本語）
├── README.en.md            # 英語版
├── INSTALL.md              # インストール手順（日本語）
├── INSTALL.en.md           # インストール手順（英語）
├── CLI_USAGE.md            # CLI の使い方（日本語）
├── LICENSE                 # MIT
├── docs/decisions/         # ADR（日本語が正本、英語は *.en.md）
├── src/                    # 拡張機能と CLI のソース
├── scripts/
│   ├── install.sh          # 拡張機能向けのビルド（Electron 向けに rebuild）
│   ├── setup-cli.sh        # CLI 向けのセットアップ（Node.js 向けに rebuild して npm link）
│   └── release.sh          # .vsix と GitHub Release の作成
├── esbuild.mjs             # バンドル設定
└── package.json
```

## ライセンス

[MIT](LICENSE)

## 免責

本ツールは、公開されていない Cursor のディスク上の保存形式に依存しています。Cursor の更新によって、一覧表示やエクスポートが動かなくなることがあります。対応していない形式や空のスレッドは、警告を出してスキップします。将来の Cursor のバージョンとの互換性は保証しないので、自己責任で利用してください。
