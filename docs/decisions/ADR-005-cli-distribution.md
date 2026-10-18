# ADR-005: CLI 配布

[English](ADR-005-cli-distribution.en.md)

- **状態:** Accepted
- **日付:** 2026-02-21（2026-10-18 書き直し）

## コンテキスト

ターミナルから `list` / `export` を手軽に実行したい。候補: `npm link` + `bin`、`npx`、スタンドアロンバイナリ（`pkg` / `nexe`）。

## 決定

- `package.json` の `bin` で `cursor-thread-tools` を登録
- グローバル導入は **`npm link`**（`scripts/setup-cli.sh` がラップ）
- スタンドアロン native バイナリは出さない

## 根拠

- `npx` は native モジュールと相性が悪い（毎回 install / ABI ぶれ）
- スタンドアロン系は native addon の同梱が弱い
- 想定利用者は Node ツールに慣れた開発者

## 影響

| 用途 | ビルド | コマンド |
|------|--------|---------|
| Cursor 内の拡張 | Electron rebuild | `bash scripts/install.sh` |
| ターミナル CLI | Node rebuild | `bash scripts/setup-cli.sh` |

インストール済み `.vsix` は独自のネイティブを同梱するため、ローカル CLI の rebuild 状態とは独立。
