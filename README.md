<p align="center">
  <img src="https://em-content.zobj.net/source/apple/391/page-facing-up_1f4c4.png" width="120" />
</p>

<h1 align="center">parsemd</h1>

<p align="center">
  <strong>parse binary docs into Claude context with /parsemd</strong>
</p>

<p align="center">
  <a href="https://github.com/ayastaga/parsemd/stargazers"><img src="https://img.shields.io/github/stars/ayastaga/parsemd?style=flat&color=yellow" alt="Stars"></a>
  <a href="https://github.com/ayastaga/parsemd/commits/main"><img src="https://img.shields.io/github/last-commit/ayastaga/parsemd?style=flat" alt="Last Commit"></a>
  <a href="LICENSE"><img src="https://img.shields.io/github/license/ayastaga/parsemd?style=flat" alt="License"></a>
</p>

<p align="center">
  <a href="#usage">Usage</a> •
  <a href="#install">Install</a> •
  <a href="#supported-formats">Formats</a> •
  <a href="#how-it-works">How It Works</a>
</p>

---

A [Claude Code](https://claude.ai/code) plugin that converts binary documents (DOCX, PDF, PPTX, XLSX, images, audio) into markdown and injects the content into Claude's context on demand.

Claude Code's built-in `@` file referencing handles plain text natively. This plugin fills the gap for binary formats that `@` cannot read.

Powered by Microsoft's [markitdown](https://github.com/microsoft/markitdown).

## Usage

> **Note:** Command syntax depends on install method. Plugin install uses `/parsemd:parsemd`, standalone install uses bare `/parsemd`. Examples below use the standalone form for brevity.

### File path syntax

All three forms are equivalent:

```
/parsemd ~/docs/report.pdf
/parsemd @~/docs/report.pdf
/parsemd @"~/docs/report.pdf"
```

Use quoted form for paths with spaces:

```
/parsemd @"~/My Documents/Q4 Report.pdf"
```

Absolute path:

```
/parsemd /tmp/export.docx
/parsemd @"/tmp/export.docx"
```

### Inject into context (no file saved)

```
/parsemd ~/docs/report.pdf
```

### Save `.md` to current working directory

```
/parsemd-save ~/docs/report.pdf
```

### Save `.md` to custom path

```
/parsemd ~/docs/report.pdf --output ~/notes/report.md
/parsemd @"~/docs/report.pdf" --output @"~/notes/report.md"
```

### Multiple files in one message

```
/parsemd ~/docs/a.pdf compare with /parsemd ~/docs/b.docx
```

Both converted in parallel and injected. Repeated files within a session are served from cache — no re-conversion.

### Show all commands

```
/parsemd-help
```

### What does NOT trigger conversion

```
use template.docx as reference format
```

No `/parsemd` trigger → no conversion. Claude processes the message as-is.

## Supported Formats

| Category   | Extensions                                            |
| ---------- | ----------------------------------------------------- |
| Documents  | `.docx` `.pdf` `.pptx` `.ppt` `.xlsx` `.xls` `.epub` |
| Archives   | `.zip`                                                |
| Images     | `.jpg` `.jpeg` `.png` `.gif` `.bmp` `.tiff`           |
| Audio      | `.wav` `.mp3` `.m4a`                                  |
| Web / Data | `.html` `.csv` `.json`                                |

Plain text (`.txt`, `.md`, `.py`, etc.) — use Claude Code's `@` directly, no plugin needed.

## Install

Requires Node.js, Python 3 with pip, and the `claude` CLI.

### Plugin install (recommended)

Commands are namespaced: `/parsemd:parsemd`, `/parsemd:parsemd-save`, `/parsemd:parsemd-help`.

```bash
pip install 'markitdown[all]'
claude plugin marketplace add ayastaga/parsemd
claude plugin install parsemd@parsemd
```

Or one line:

```bash
curl -fsSL https://raw.githubusercontent.com/ayastaga/parsemd/main/install.sh | bash
```

Restart Claude Code after installing.

### Standalone install (bare `/parsemd`)

Adds the hook and command directly to your user config. No namespace prefix.

```bash
curl -fsSL https://raw.githubusercontent.com/ayastaga/parsemd/main/install.sh | bash -s -- --standalone
```

Or clone the repo and run locally (hook is symlinked — updates apply without reinstalling):

```bash
git clone https://github.com/ayastaga/parsemd.git
cd parsemd
./install.sh --standalone
```

#### Manual standalone setup

**1. Install markitdown:**

```bash
pip install 'markitdown[all]'
```

**2. Download hook script:**

```bash
mkdir -p ~/.claude/hooks
curl -fsSL https://raw.githubusercontent.com/ayastaga/parsemd/main/hooks/parsemd-hook.js \
  -o ~/.claude/hooks/parsemd-hook.js
```

**3. Add `/parsemd` command:**

```bash
mkdir -p ~/.claude/commands
printf -- '---\ndescription: Parse binary documents into markdown context\n---\n\n/parse $ARGUMENTS\n' \
  > ~/.claude/commands/parsemd.md
```

**4. Register hook in `~/.claude/settings.json`:**

```json
{
  "hooks": {
    "UserPromptSubmit": [
      {
        "matcher": "/parse",
        "hooks": [
          {
            "type": "command",
            "command": "node \"/Users/YOU/.claude/hooks/parsemd-hook.js\"",
            "timeout": 35
          }
        ]
      }
    ]
  }
}
```

Replace `/Users/YOU` with your actual home directory. Restart Claude Code.

## How It Works

1. Plugin registers a `UserPromptSubmit` hook with matcher `/parse` — fires only on messages containing that trigger.
2. Hook scans the prompt for `/parse <path>` patterns before Claude sees the message.
3. Matching files are converted via `markitdown` (in parallel for multiple files) and injected as `additionalContext` — Claude receives clean markdown, never the raw binary.
4. Repeated files are served from a per-session cache keyed by file path + mtime.

A brief system message confirms conversion:

```
Parsed: report.pdf → markdown (4,231 chars). Injected into context.
```

## License

MIT
