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

Claude Code's built-in `@` file referencing handles text files natively. This plugin fills the gap for binary formats that `@` cannot read.

Powered by Microsoft's [markitdown](https://github.com/microsoft/markitdown).

## Usage

> **Note:** Command syntax depends on install method. Plugin install uses `/parsemd:parsemd`, standalone install uses bare `/parsemd`. See [Install](#install).

Convert and inject into context:

```
/parsemd:parsemd ~/docs/report.pdf
```

Save `.md` alongside the source file:

```
/parsemd:parsemd ~/docs/report.docx --output
```

Save `.md` in the current working directory of your Claude session:

```
/parsemd:parsemd ~/docs/report.docx --output-save
```

Save `.md` to a custom path:

```
/parsemd:parsemd ~/docs/report.docx -o ~/notes/report-notes.md
```

Multiple files in one message:

```
/parsemd:parsemd ~/docs/a.pdf compare with /parsemd:parsemd ~/docs/b.pdf
```

Show all commands:

```
/parsemd:parsemd-help
```

### What does NOT trigger conversion

```
use template.docx as reference format
```

No `/parsemd` trigger → no conversion. Claude processes the message as-is.

## Supported Formats

| Category  | Extensions                                            |
| --------- | ----------------------------------------------------- |
| Documents | `.docx` `.pdf` `.pptx` `.ppt` `.xlsx` `.xls` `.epub` |
| Archives  | `.zip`                                                |
| Images    | `.jpg` `.jpeg` `.png` `.gif` `.bmp` `.tiff`           |
| Audio     | `.wav` `.mp3` `.m4a`                                  |

Text files (`.txt`, `.md`, `.py`, `.csv`, etc.) are intentionally excluded — Claude Code's `@` already handles those natively.

## Install

Requires Node.js, Python 3 with pip, and the `claude` CLI.

### Plugin install (recommended)

Commands are namespaced: `/parsemd:parsemd`, `/parsemd:parsemd-help`.

```bash
pip install 'markitdown[all]'
claude plugin marketplace add github:ayastaga/parsemd
claude plugin install parsemd@parsemd
```

Or one line:

```bash
curl -fsSL https://raw.githubusercontent.com/ayastaga/parsemd/main/install.sh | bash
```

Restart Claude Code after installing.

### Standalone install (bare `/parsemd`)

Adds the hook and command directly to your user config. Commands have no namespace prefix.

```bash
curl -fsSL https://raw.githubusercontent.com/ayastaga/parsemd/main/install.sh | bash -s -- --standalone
```

Or manually:

**1. Install markitdown:**

```bash
pip install 'markitdown[all]'
```

**2. Download hook script:**

```bash
mkdir -p ~/.claude/hooks
curl -fsSL https://raw.githubusercontent.com/ayastaga/parsemd/main/hooks/markitdown-hook.js \
  -o ~/.claude/hooks/parsemd-hook.js
```

**3. Add `/parsemd` command:**

```bash
mkdir -p ~/.claude/commands
curl -fsSL https://raw.githubusercontent.com/ayastaga/parsemd/main/commands/parsemd.md \
  -o ~/.claude/commands/parsemd.md
```

**4. Register hook in `~/.claude/settings.json`:**

```json
{
  "hooks": {
    "UserPromptSubmit": [
      {
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

Replace `/Users/YOU` with your actual home directory path. Restart Claude Code.

## How It Works

1. Plugin registers a `UserPromptSubmit` hook.
2. On every message, the hook scans for `/parse <path>` patterns before Claude sees the prompt.
3. Matching files are converted via `markitdown` and injected as `additionalContext` — Claude receives clean markdown, never the raw binary.

A brief system message confirms conversion:

```
Parsed: report.pdf → markdown (4,231 chars). Injected into context.
```

## License

MIT
