# cursor-thread-tools

[日本語](README.md)

Export Cursor Composer / Agent thread conversations to Markdown, either as a **Cursor extension** or from the terminal with a **CLI**.

Cursor's built-in "Export Transcript" requires a GUI save dialog and is not available from the Command Palette. This tool opens Cursor's local SQLite store (`state.vscdb`) in **read-only** mode and reconstructs transcripts from it. Reading conversations does not use any of Cursor's private APIs.

**Status:** maintenance mode. No major new features are planned. Verified against **Cursor 3.25.x (Electron 42)**. The extension is not published to the VS Code / Cursor Marketplace; install it from a `.vsix` release or build it from source.

## Features

- List Composer threads (name, message count, Agent / Chat mode, creation date)
- Export one thread or many to Markdown, with thinking blocks optionally included
- Change the export directory from the Command Palette
- Automatically add the exported file to Composer as an `@` reference (this uses an internal Cursor command; if it fails, the export still completes)
- Background auto-save (off by default)
- CLI: `cursor-thread-tools list` / `export`

## Quick start

### Extension (recommended for most users)

1. Download the `.vsix` for your platform from [GitHub Releases](https://github.com/stlwolf/cursor-thread-tools/releases).
2. In Cursor, run `Cmd+Shift+P` → `Extensions: Install from VSIX...`, select the downloaded file, then reload the window.
3. Run `Cmd+Shift+P` → `Thread Tools: List Threads` or `Thread Tools: Export Thread to Markdown`.

The `.vsix` bundles the native `better-sqlite3` module, so the file must match your OS and architecture. Releases currently ship an Apple Silicon build (`darwin-arm64`) only.

### CLI

```bash
npm install
bash scripts/setup-cli.sh   # rebuild for Node.js and run npm link

cursor-thread-tools list
cursor-thread-tools export --all --since 7d
```

For full install and rebuild steps, see [INSTALL.en.md](INSTALL.en.md) (Japanese original: [INSTALL.md](INSTALL.md)). CLI options and examples are in [CLI_USAGE.md](CLI_USAGE.md) (Japanese only).

## Requirements

- macOS (Windows / Linux paths are supported but untested)
- Node.js **20+** for the CLI and source builds (**22+** recommended when using `@electron/rebuild`)
- Cursor installed and launched at least once (`state.vscdb` must exist)
- Extension build target: **Electron 42.10.0** (override with `ELECTRON_VERSION=...`)

## How it works

1. Open `state.vscdb` (SQLite) read-only.
2. Read `composerData:*` metadata from the `cursorDiskKV` table.
3. Decode `conversationState` (protobuf) and follow the `agentKv:blob` entries it references to get the message text.
4. Write the result as Markdown.

Design decisions (SQLite library, protobuf parsing, packaging, the Electron / Node.js ABI split) are recorded in [`docs/decisions/`](docs/decisions/).

## Project layout

```text
cursor-thread-tools/
├── README.md               # Japanese (primary)
├── README.en.md            # English (this file)
├── INSTALL.md              # Install guide (Japanese)
├── INSTALL.en.md           # Install guide (English)
├── CLI_USAGE.md            # CLI usage (Japanese)
├── LICENSE                 # MIT
├── docs/decisions/         # ADRs (Japanese primary, English in *.en.md)
├── src/                    # Extension and CLI sources
├── scripts/
│   ├── install.sh          # Extension build (rebuild for Electron)
│   ├── setup-cli.sh        # CLI setup (rebuild for Node.js + npm link)
│   └── release.sh          # Build the .vsix and create a GitHub Release
├── esbuild.mjs             # Bundler config
└── package.json
```

## License

[MIT](LICENSE)

## Disclaimer

This tool depends on Cursor's undocumented on-disk format. Cursor updates can break listing or export. Unsupported or empty thread formats are skipped with a warning instead of failing the whole run. Use at your own risk; compatibility with future Cursor versions is not guaranteed.
