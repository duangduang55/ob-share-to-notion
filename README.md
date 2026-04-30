# Share to Notion (Block Update)

An Obsidian plugin that syncs markdown files to Notion databases. Supports **multiple database targets**, **custom property mapping**, **auto-sync**, and **image/file uploads**.

## Key Modification

This is a fork of [jxpeng98/obsidian-to-NotionNext](https://github.com/jxpeng98/obsidian-to-NotionNext) (v2.8.4). The core difference:

| Behavior | Original | This Fork |
|----------|----------|-----------|
| Update strategy | Delete page → Create new page | Update page properties → Clear blocks → Append new blocks |
| Page ID on update | Changes (new page created) | Preserved (same page) |
| Notion trash | Accumulates deleted pages | No trash buildup |

The **update flow** (`upsertGeneral` / `upsertNext` / `upsertCustom`):
1. `PATCH /v1/pages/{pageId}` — update page properties (title, tags, cover, etc.)
2. `GET /v1/blocks/{pageId}/children` + `DELETE /v1/blocks/{blockId}` — clear all existing blocks
3. `PATCH /v1/blocks/{pageId}/children` — append all new blocks

This avoids the Notion API's lack of a native "replace all blocks" endpoint.

Key code: `scripts/Upload2Notion.ts` → `clearPageBlocks()` + `appendExtraBlocks()`

## Features

- **Multiple database targets**: General, NotionNext, and Custom datasets
- **Custom property mapping**: Map Obsidian frontmatter to Notion database properties (title, multi_select, date, select, rich_text, etc.)
- **Auto-sync**: Automatically sync on file save (configurable delay)
- **Image & file upload**: Uploads local images/files to Notion
- **Markdown → Blocks**: Converts headings, lists, code blocks, tables, callouts, etc. to Notion native blocks
- **Multi-language**: English, Chinese, Japanese

## Installation

### From Obsidian Community Plugins (if listed)

Search "Share to Notion (Block Update)" in Community Plugins.

### Manual install (BRAT)

1. Install the [BRAT](https://obsidian.md/plugins?id=obsidian42-brat) plugin
2. Add `https://github.com/duangduang55/ob-share-to-notion` as a Beta Plugin
3. Reload Obsidian

### Manual install (from releases)

1. Download the latest `main.js` and `manifest.json` from [Releases](https://github.com/duangduang55/ob-share-to-notion/releases)
2. Create folder `YOUR_VAULT/.obsidian/plugins/ob-share-to-notion/`
3. Copy both files into that folder
4. Reload Obsidian and enable the plugin

## Setup

1. Create a Notion Integration at https://www.notion.so/my-integrations
2. Copy the Internal Integration Secret (starts with `ntn_` or `secret_`)
3. Share your target database with the Integration (Database → Share → Invite)
4. Extract the Database ID from the URL: `https://www.notion.so/{workspace}/{32-char-uuid}?v=...`
5. In Obsidian, configure the plugin with:
   - **Notion API Token**: Your integration secret
   - **Database ID**: The 32-character UUID
   - **Custom Properties** (optional): Map your frontmatter fields to Notion database properties

## Usage

### Commands

- **Share to Notion** (`Ctrl/Cmd + P` → "Share to Notion") — Sync the current note
- **Auto-sync** — Enable in settings to sync automatically on file save

### Frontmatter

The plugin reads frontmatter for metadata:

```yaml
---
title: My Note
tags: [tag1, tag2]
cover: https://example.com/image.jpg
autosync-database: notebook  # which database target to use for auto-sync
---
```

After first sync, the plugin adds a `NotionID-{abName}` field to your frontmatter to track the linked Notion page.

## Development

```bash
# Install dependencies
npm install

# Build
npm run build

# The output is main.js
```

## Credits

- [EasyChris/obsidian-to-notion](https://github.com/EasyChris/obsidian-to-notion) — original plugin
- [jxpeng98/obsidian-to-NotionNext](https://github.com/jxpeng98/obsidian-to-NotionNext) — feature enhancements and NotionNext support

## License

MIT
