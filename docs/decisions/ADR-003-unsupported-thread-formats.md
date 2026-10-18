# ADR-003: 未対応 / 空フォーマットはスキップ

[English](ADR-003-unsupported-thread-formats.en.md)

- **状態:** Accepted
- **日付:** 2026-02-21（2026-10-18 書き直し）

## コンテキスト

一部スレッドの `conversationState` からはターンが取れない（空 / `"~"`、未知の保存パスなど）。

## 決定

**Cursor の全形式を積極的に逆引きしない。** 該当スレッドは明示警告してスキップし、残りはエクスポートを続ける。

## 根拠

- 空スレッドは出力するものがない
- 未知の保存レイアウトは Cursor 内部都合で、追従は作業が際限なく広がる
- 失敗を柔らかく扱うことで `export --all` が使える

## 影響

- 一部 ID で「newer data format not yet supported」相当の警告が出ることがある
- 新パス対応は conversation-state 参照箇所に局所化できる
