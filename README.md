# cursor-thread-tools

[English](README.en.md)

Cursor の Composer / Agent スレッド会話を Markdown にエクスポートするツールです。**Cursor 拡張**と **CLI** の両方があります。

Cursor 標準の「Export Transcript」は GUI の保存ダイアログ必須で、コマンドパレットにも出ません。本ツールはローカルの SQLite（`state.vscdb`）を**読み取り専用**で開き、Cursor の非公開 API に依存せずトランスクリプトを再構成します。

**ステータス:** メンテ / アーカイブ寄り。検証環境は **Cursor 3.25.x（Electron 42）**。VS Code / Cursor Marketplace には未公開。`.vsix` Release またはソースビルドで導入します。

## 機能

- スレッド一覧（名前、メッセージ数、Agent/Chat、日時）
- スレッドの Markdown エクスポート（thinking ブロックは任意）
- コマンドパレットから出力先ディレクトリ変更
- エクスポート後、Composer へ `@` 参照として追加（任意・成功時）
- バックグラウンド自動保存（既定オフ）
- CLI: `cursor-thread-tools list` / `export`

## クイックスタート

### 拡張機能（多くの場合はこちら）

1. [GitHub Releases](https://github.com/stlwolf/cursor-thread-tools/releases) から環境に合う `.vsix` をダウンロード
2. Cursor → `Cmd+Shift+P` → `Extensions: Install from VSIX...` → ウィンドウをリロード
3. `Cmd+Shift+P` → `Thread Tools: List Threads` / `Export Thread to Markdown`

`.vsix` にはネイティブの `better-sqlite3` が含まれるため、OS/アーキテクチャが一致するファイルを使ってください（例: `darwin-arm64`）。

### CLI

```bash
npm install
bash scripts/setup-cli.sh   # Node 向け rebuild + npm link

cursor-thread-tools list
cursor-thread-tools export --all --since 7d
```

詳細: [INSTALL.md](INSTALL.md) · [INSTALL.en.md](INSTALL.en.md) · [CLI_USAGE.md](CLI_USAGE.md)

## 要件

- macOS（Windows / Linux はパス対応あり・未テスト）
- CLI / ソースビルド: Node.js **20+**（`@electron/rebuild` は **22+** 推奨）
- Cursor を一度は起動済み（`state.vscdb` が存在すること）
- 現行ビルド対象: **Electron 42.10.0**（`ELECTRON_VERSION=...` で上書き可）

## 仕組み（概要）

1. `state.vscdb` を read-only で開く
2. `cursorDiskKV` から `composerData:*` メタデータを読む
3. `conversationState`（protobuf）→ `agentKv:blob` → メッセージ本文
4. Markdown を出力

設計判断は [`docs/decisions/`](docs/decisions/) を参照。

## ディレクトリ構成

```text
cursor-thread-tools/
├── README.md               # 本ファイル（日本語）
├── README.en.md            # English
├── LICENSE                 # MIT
├── docs/decisions/         # ADR（日本語メイン / *.en.md）
├── src/
├── scripts/install.sh
├── scripts/setup-cli.sh
├── INSTALL.md
└── package.json
```

## ライセンス

[MIT](LICENSE)

## 免責

本ツールは Cursor の非公開オンディスク形式に依存します。Cursor の更新で一覧・エクスポートが壊れることがあります。未対応 / 空の形式は警告してスキップします。自己責任で利用してください。将来バージョンとの互換性は保証しません。
