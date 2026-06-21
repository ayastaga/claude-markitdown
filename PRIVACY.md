# Privacy Policy

**Last updated:** June 20, 2026

## Overview

parsemd is a Claude Code plugin that converts binary documents into markdown. This policy describes how the plugin handles data.

## Data Collection

parsemd does **not** collect, store, or transmit any personal data. The plugin has no backend, no analytics, and no telemetry.

## How It Works

When you run `/parsemd`, the plugin:

1. Reads the file you specify from your local filesystem
2. Converts it to markdown using [markitdown](https://github.com/microsoft/markitdown), which runs entirely on your machine
3. Injects the resulting markdown into your Claude Code session context

All processing is local. No file contents, metadata, or usage data leave your device.

## Third-Party Tools

parsemd uses Microsoft's [markitdown](https://github.com/microsoft/markitdown) library for document conversion. markitdown runs locally. Refer to [Microsoft's privacy policy](https://privacy.microsoft.com/en-us/privacystatement) for information about the library itself.

If you configure markitdown with an external LLM client (e.g. for image descriptions), data may be sent to that provider. That is optional and not part of parsemd's default behavior.

## Claude Code Context

Content injected into your Claude Code session is subject to [Anthropic's Privacy Policy](https://www.anthropic.com/legal/privacy). parsemd does not control how Anthropic processes session data.

## Data Retention

parsemd stores nothing. It maintains an in-session cache to avoid re-converting the same file twice within a single session. This cache exists only in memory and is cleared when the session ends.

## Contact

Questions or concerns: open an issue at [github.com/ayastaga/parsemd](https://github.com/ayastaga/parsemd/issues).
