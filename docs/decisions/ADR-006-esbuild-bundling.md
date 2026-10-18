# ADR-006: esbuild バンドル

[English](ADR-006-esbuild-bundling.en.md)

- **状態:** Accepted
- **日付:** 2026-02-21（2026-10-18 書き直し）

## コンテキスト

初期は `tsc` で `out/` に複数ファイルを出していた。配布とロードのため、拡張と CLI のエントリを単一ファイルにまとめたい。

## 決定

- **esbuild** でバンドル（`extension.ts` → `out/extension.js`、`cli.ts` → `out/cli.js`）
- `vscode` と `better-sqlite3` は **external**
- `format: 'cjs'`、`platform: 'node'`、sourcemap あり
- CLI の shebang は esbuild `banner` のみ
- 型チェックは `tsc --noEmit`

## 根拠

- VS Code 拡張のバンドル案内に沿う
- native モジュールを esbuild でインラインすると `__dirname` / binding 解決が壊れる

## 影響

- 実行時は `node_modules/better-sqlite3`（または `.vsix` 内のコピー）が必要
