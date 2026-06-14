<p align="center">
  <img src="https://em-content.zobj.net/source/apple/391/page-facing-up_1f4c4.png" width="120" />
</p>

<h1 align="center">claude-markitdown</h1>

<p align="center">
  <strong>parse binary docs into context with /parse</strong>
</p>

<p align="center">
  <a href="https://github.com/ayastaga/claude-markitdown/stargazers"><img src="https://img.shields.io/github/stars/ayastaga/claude-markitdown?style=flat&color=yellow" alt="Stars"></a>
  <a href="https://github.com/ayastaga/claude-markitdown/commits/main"><img src="https://img.shields.io/github/last-commit/ayastaga/claude-markitdown?style=flat" alt="Last Commit"></a>
  <a href="LICENSE"><img src="https://img.shields.io/github/license/ayastaga/claude-markitdown?style=flat" alt="License"></a>
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

```
/parse ~/docs/report.pdf
```

→ Converts and injects into context. Claude can then answer questions about it.

```
/parse ~/docs/report.docx --output
```

→ Converts, injects into context, and saves `report.md` alongside the source file.

```
/parse ~/docs/report.docx -o ~/notes/report-notes.md
```

→ Converts, injects into context, and saves to a custom path.

```
/parse ~/docs/a.pdf compare with /parse ~/docs/b.pdf
```

→ Multiple files in one message — both converted and injected.

### What does NOT trigger conversion

```
use template.docx as reference format
```

No `/parse` trigger → no conversion. Claude processes the message as-is.

## Supported Formats

| Category  | Extensions                                            |
| --------- | ----------------------------------------------------- |
| Documents | `.docx` `.pdf` `.pptx` `.ppt` `.xlsx` `.xls` `.epub` |
| Archives  | `.zip`                                                |
| Images    | `.jpg` `.jpeg` `.png` `.gif` `.bmp` `.tiff`           |
| Audio     | `.wav` `.mp3` `.m4a`                                  |

Text files (`.txt`, `.md`, `.py`, `.csv`, etc.) are intentionally excluded — Claude Code's `@` already handles those natively.

## Install

One line.

```bash
curl -fsSL https://raw.githubusercontent.com/ayastaga/claude-markitdown/main/install.sh | bash
```

Needs Node.js, Python 3 with pip, and the `claude` CLI. Restart Claude Code after installing.

**Trigger:** type `/parse <path>` in any message.

### Manual installation

**1. Install markitdown:**

```bash
pip install 'markitdown[all]'
```

**2. Register the marketplace:**

```bash
claude plugin marketplace add github:ayastaga/claude-markitdown
```

**3. Install the plugin:**

```bash
claude plugin install claude-markitdown@claude-markitdown
```

Restart Claude Code.

## How It Works

1. Plugin registers a `UserPromptSubmit` hook.
2. On every message, the hook scans for `/parse <path>` patterns before Claude sees the prompt.
3. Matching files are converted via `markitdown` and injected as `additionalContext` — Claude receives clean markdown, never the raw binary.

A brief system message confirms conversion: `Parsed: report.pdf → markdown (4,231 chars). Injected into context.`

## License

MIT
