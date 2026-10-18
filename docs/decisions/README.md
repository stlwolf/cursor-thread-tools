# Decisions（ADR）

[English](README.en.md)

長期的な技術判断の根拠。プロセス履歴（plans / episodes）は意図的に置いていません。

各 ADR は日本語が正本、英語は `*.en.md` です。

| ADR | 内容 |
|-----|------|
| [ADR-001](ADR-001-sqlite-library.md) | SQLite ライブラリ（`better-sqlite3`） |
| [ADR-002](ADR-002-raw-protobuf.md) | raw protobuf wire-format パーサー |
| [ADR-003](ADR-003-unsupported-thread-formats.md) | 未対応 / 空フォーマットはスキップ |
| [ADR-004](ADR-004-native-module-distribution.md) | ネイティブモジュール + VSIX 配布（Electron 42） |
| [ADR-005](ADR-005-cli-distribution.md) | CLI 配布（`npm link` + `bin`） |
| [ADR-006](ADR-006-esbuild-bundling.md) | esbuild バンドル |
| [ADR-007](ADR-007-auto-save-opt-in.md) | 自動保存はオプトイン既定 |
