# ADR-004: ネイティブモジュール配布（Electron + VSIX）

[English](ADR-004-native-module-distribution.en.md)

- **状態:** Accepted
- **日付:** 2026-02-21（2026-10-18 書き直し・Electron 対象更新）

## コンテキスト

`better-sqlite3` はネイティブ `.node` を含む。Extension Host は **Electron**、CLI は **Node** で ABI が異なる。Cursor は信頼できる `electronVersion` フィールドを公開していない。

v13 以降、better-sqlite3 は `prebuilds/<platform>-<arch>.node` を優先する（Electron rebuild が該当 prebuild を上書きし、forge-meta に ABI を記録）。

## 決定

- `scripts/install.sh` で `npm install` → esbuild → `@electron/rebuild`
- 既定 Electron: **`42.10.0`**（最終検証時の Cursor 3.25.x）。`ELECTRON_VERSION` または引数で上書き
- Electron 42 / 現行 V8 向けに **`better-sqlite3` ^13** が必要（v12 はコンパイル失敗）
- 再ビルド済みネイティブを含む OS/arch 固有 `.vsix` を配布
- 毎回の `npm install` で重い `postinstall` rebuild はしない

## 根拠

- 明示的な rebuild の方がデバッグしやすい
- プラットフォーム固有 VSIX は native addon の現実的な配布形
- 以前の Electron 39 + better-sqlite3 v12 と同型。Cursor が Electron 42 になったタイミングで再バンプ

## 影響

- 拡張と CLI で同一 `node_modules` ABI を共有できない（ADR-005）
- `install.sh` 後に CLI を使うには `setup-cli.sh` / `npm rebuild` が必要
- 古い Cursor / Electron は新規 Release のサポート対象外
