# ADR-001: SQLite ライブラリ — better-sqlite3

[English](ADR-001-sqlite-library.en.md)

- **状態:** Accepted
- **日付:** 2026-02-20（公開用に 2026-10-18 書き直し）

## コンテキスト

Cursor は Composer データを `state.vscdb`（SQLite）に保存する。拡張と CLI は、Cursor 起動中でもこの DB を**読み取り専用**で開く必要がある。

候補: `better-sqlite3`、`vscode-node-sqlite3`、`sql.js`（WASM）。

## 決定

**`better-sqlite3` を採用する。**

## 根拠

- 同期 API が VS Code コマンドハンドラに合う
- `readonly` + `busy_timeout` でライブ `state.vscdb` の読み取り実績あり
- `sql.js` は DB 全体をメモリに載せるため、数 GB 級では危険
- `vscode-node-sqlite3` は native rebuild が恒久的に失敗した場合の理論上のフォールバックに留める

## 影響

- ネイティブアドオンのため、Electron（拡張）と Node（CLI）で **ABI 別ビルド**が必要
- `.vsix` には OS/arch 固有バイナリを同梱する（ADR-004）
