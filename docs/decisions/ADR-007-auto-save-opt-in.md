# ADR-007: 自動保存の既定はオフ（オプトイン）

[English](ADR-007-auto-save-opt-in.en.md)

- **状態:** Accepted
- **日付:** 2026-02-21（2026-10-18 書き直し）

## コンテキスト

更新スレッドのバックグラウンド保存はアーカイブに便利だが、既定でワークスペースへ Markdown を書くと驚きや他ツールとの二重保存になりうる。

## 決定

`cursorThreadTools.autoSave.intervalMinutes` の既定を **`0`（無効）** にする。有効化はユーザー明示。

## 根拠

- 最小驚き: 意図しない `.thread-exports/` の肥大化を避ける
- センシティブな会話の黙った保存を避ける
- 不要な定期 DB 読みを避ける

## 影響

- interval ≤ 0 なら `setupAutoSave()` は何もしない
- 拡張は起動時に activate するが、タイマーは設定時のみ
