# cursor-thread-tools CLI

Cursor の Composer スレッドの会話データを、ターミナルから扱うための CLI です。

## セットアップ

```bash
cd /path/to/cursor-thread-tools

# Node.js 向けビルド（CLI 利用時）
npm install
npm run build
npm rebuild better-sqlite3

# グローバルコマンドとして登録
npm link
```

> Electron 向けリビルド（`scripts/install.sh`）を実行した後に CLI を使う場合は、先に `npm rebuild better-sqlite3` を実行してください。

## コマンド

### `list` — スレッド一覧

```bash
# テキスト出力（名前, モード, メッセージ数, 日時, composerId）
cursor-thread-tools list

# JSON 出力（パイプ処理向き）
cursor-thread-tools list --json

# jq で Agent モードのスレッドだけ抽出
cursor-thread-tools list --json | jq '[.[] | select(.isAgentic == true)]'

# 名前で grep
cursor-thread-tools list --json | jq -r '.[] | "\(.name)\t\(.composerId)"' | grep -i "login"
```

### `export` — Markdown エクスポート

```bash
# 特定のスレッドをエクスポート（composerId を指定）
cursor-thread-tools export <composerId>

# 全スレッドをエクスポート
cursor-thread-tools export --all

# 直近24時間のスレッドだけエクスポート
cursor-thread-tools export --all --since 24h

# 直近7日間
cursor-thread-tools export --all --since 7d

# 出力先を指定
cursor-thread-tools export --all --output-dir ~/Desktop/thread-exports

# thinking ブロックを除外（ファイルサイズ削減）
cursor-thread-tools export --all --no-thinking

# ファイル名フォーマットを変更
cursor-thread-tools export --all --format "{date}_{name}"
```

## オプション一覧

| オプション | サブコマンド | 説明 | デフォルト |
|-----------|------------|------|-----------|
| `--json` | list | JSON 形式で出力 | false |
| `--all` | export | 全スレッド対象 | false |
| `--since <duration>` | export --all | 期間フィルタ（`24h`, `7d`, `30m`） | なし |
| `--no-thinking` | export | thinking ブロックを除外 | false（含む） |
| `--output-dir <path>` | export | 出力ディレクトリ | `.thread-exports` |
| `--format <pattern>` | export | ファイル名テンプレート | `{name}_{date}` |
| `--app-name <name>` | list, export | アプリ名（`Cursor` または `Code`） | `Cursor` |
| `--help` | - | ヘルプ表示 | - |

### `--format` プレースホルダ

| プレースホルダ | 内容 |
|-------------|------|
| `{name}` | スレッド名（60 文字まで。英数字、日本語、`_`、`-` 以外の文字は `_` に置き換える） |
| `{date}` | エクスポート日（`YYYY-MM-DD`） |
| `{id}` | エクスポートした時刻から作る短い文字列（スレッドの ID ではなく、実行するたびに変わる） |

## よく使うパターン

### 日次バックアップ

```bash
# cron や launchd で毎日実行
cursor-thread-tools export --all --since 24h --output-dir ~/Documents/cursor-threads
```

### composerId の取得

```bash
# list で一覧を表示し、右端の UUID が composerId
cursor-thread-tools list
# 出力例:
# ログイン画面のバグ修正  [Agent]  42 msgs  2026/1/15 10:30:00  a1b2c3d4-e5f6-4789-abcd-ef0123456789

# JSON + jq で名前から composerId を引く
cursor-thread-tools list --json | jq -r '.[] | select(.name | test("ログイン")) | .composerId'
```

### 特定スレッドの単体エクスポート

```bash
# composerId を指定
cursor-thread-tools export a1b2c3d4-e5f6-4789-abcd-ef0123456789
# → .thread-exports/ログイン画面のバグ修正_2026-01-15.md
```

### git 連携

```bash
# ワークスペースの .thread-exports/ に出力して git 管理
cursor-thread-tools export --all --output-dir .thread-exports
git add .thread-exports/
git commit -m "docs: export thread conversations"
```

## 出力形式

エクスポートされる Markdown の構造:

```markdown
# スレッド名
_Exported on 2026-01-15 from Cursor Thread Tools_

---

**User**

ユーザーの発言テキスト

---

**Assistant**

<details>
<summary>Thinking</summary>

（thinking ブロック、--no-thinking で除外可能）

</details>

アシスタントの応答テキスト

---
```

## npm link を使わない場合

```bash
# プロジェクトディレクトリから直接実行
node out/cli.js list
node out/cli.js export --all --since 24h
```

## トラブルシューティング

### `MODULE_NOT_FOUND` / `NODE_MODULE_VERSION mismatch`

```bash
# Electron 向けリビルド後に CLI を使う場合
npm rebuild better-sqlite3
```

### `state.vscdb not found`

Cursor が一度も起動されていないか、DB のパスが想定と違います。DB の場所は次のとおりです。
- macOS: `~/Library/Application Support/Cursor/User/globalStorage/state.vscdb`
- Windows: `%APPDATA%\Cursor\User\globalStorage\state.vscdb`
- Linux: `~/.config/Cursor/User/globalStorage/state.vscdb`

### `SQLITE_BUSY` / `SQLITE_LOCKED`

Cursor が DB に書き込んでいる最中です。`busy_timeout=3000`（3 秒）を超えると、ツールは WAL ファイルを含めて DB を一時コピーし、そのコピーから読みます。通常はリトライの必要はありません。
