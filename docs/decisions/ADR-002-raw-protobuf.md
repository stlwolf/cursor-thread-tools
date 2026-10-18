# ADR-002: raw protobuf wire-format パーサー

[English](ADR-002-raw-protobuf.en.md)

- **状態:** Accepted
- **日付:** 2026-02-21（2026-10-18 書き直し）

## コンテキスト

メッセージ本文は `agentKv:blob` に protobuf として入る。候補は `.proto` 生成、`@bufbuild/protobuf` / `protobufjs`、または小さな自前 wire パーサー。

## 決定

**依存ゼロの raw wire-format パーサー**を `src/proto/decoder.ts` に実装する。

## 根拠

- 必要なフィールドが少ない（text、blob ID、steps）
- Cursor 公式の `.proto` はなく、フルスキーマの逆引きはコストが高い
- 実データで raw パースが通った以上、追加ライブラリの旨味は薄い

## 影響

- Cursor のスキーマ変更時は `decoder.ts` のフィールド番号更新が必要
- `package.json` に protobuf ランタイムを追加しない
