# Share to Notion (Block Update)

将 Obsidian Markdown 文件同步到 Notion 数据库的插件。支持**多数据库目标**、**自定义属性映射**、**自动同步**和**图片/文件上传**。

An Obsidian plugin that syncs markdown files to Notion databases. Supports multiple database targets, custom property mapping, auto-sync, and image/file uploads.

---

## 核心修改 / Key Modification

本仓库是 [jxpeng98/obsidian-to-NotionNext](https://github.com/jxpeng98/obsidian-to-NotionNext) (v2.8.4) 的 fork。核心区别：

This is a fork of jxpeng98/obsidian-to-NotionNext (v2.8.4). The core difference:

| 行为 / Behavior | 原版 / Original | 本 Fork / This Fork |
|----------|----------|-----------|
| 更新策略 / Update strategy | 删除页面 → 新建页面 / Delete page → Create new page | 更新属性 → 清空 blocks → 追加新 blocks / Update properties → Clear blocks → Append blocks |
| 页面 ID 更新后 / Page ID on update | 变化（新建页面） / Changes (new page created) | 保持不变 / Preserved (same page) |
| Notion 回收站 / Notion trash | 不断堆积删除的页面 / Accumulates deleted pages | 无残留 / No trash buildup |

**更新流程** (`upsertGeneral` / `upsertNext` / `upsertCustom`):

1. `PATCH /v1/pages/{pageId}` — 更新页面属性（标题、标签、封面等）/ update page properties
2. `GET /v1/blocks/{pageId}/children` + `DELETE /v1/blocks/{blockId}` — 清空所有现有 blocks / clear all existing blocks
3. `PATCH /v1/blocks/{pageId}/children` — 追加所有新 blocks / append all new blocks

这解决了 Notion API 没有原生"替换所有 blocks"接口的问题。关键代码：`scripts/Upload2Notion.ts` → `clearPageBlocks()` + `appendExtraBlocks()`

---

## 功能 / Features

- **多数据库目标** / Multiple database targets: General、NotionNext、Custom 三种数据集
- **自定义属性映射** / Custom property mapping: 将 Obsidian frontmatter 映射到 Notion 数据库属性（title、multi_select、date、select、rich_text 等）
- **自动同步** / Auto-sync: 文件保存时自动同步（可配置延迟）
- **图片 & 文件上传** / Image & file upload: 自动上传本地图片和文件到 Notion
- **Markdown → Blocks**: 将标题、列表、代码块、表格、引用等转换为 Notion 原生 Block
- **多语言** / Multi-language: 简体中文、English、日本語

---

## 安装 / Installation

### 从 Obsidian 社区插件安装（如果已上架）

在社区插件中搜索 "Share to Notion (Block Update)"。

### 使用 BRAT 安装 / Manual install (BRAT)

1. 安装 [BRAT](https://obsidian.md/plugins?id=obsidian42-brat) 插件
2. 添加 `https://github.com/duangduang55/ob-share-to-notion` 作为 Beta 插件
3. 重载 Obsidian

### 手动安装 / Manual install (from releases)

1. 从 [Releases](https://github.com/duangduang55/ob-share-to-notion/releases) 下载最新的 `main.js` 和 `manifest.json`
2. 创建文件夹 `YOUR_VAULT/.obsidian/plugins/ob-share-to-notion/`
3. 将两个文件复制到该文件夹
4. 重载 Obsidian 并启用插件

---

## 配置 / Setup

1. 前往 https://www.notion.so/my-integrations 创建一个 Notion Integration
2. 复制 Internal Integration Secret（以 `ntn_` 或 `secret_` 开头）
3. 在 Notion 数据库中邀请该 Integration（数据库 → 右上角 Share → Invite）
4. 从 URL 中提取 Database ID：`https://www.notion.so/{workspace}/{32位UUID}?v=...`
5. 在 Obsidian 插件设置中填写：
   - **Notion API Token**: 你的 Integration Secret
   - **Database ID**: 32 位 UUID
   - **自定义属性 / Custom Properties**（可选）: 将 frontmatter 字段映射到 Notion 数据库属性

---

## 使用 / Usage

### 命令 / Commands

- **Share to Notion** (`Ctrl/Cmd + P` → "Share to Notion") — 同步当前笔记 / Sync the current note
- **自动同步 / Auto-sync** — 在设置中开启，保存文件时自动同步

### Frontmatter

插件从 frontmatter 读取元数据 / The plugin reads frontmatter for metadata:

```yaml
---
title: My Note
tags: [tag1, tag2]
cover: https://example.com/image.jpg
autosync-database: notebook  # 指定自动同步使用的数据库目标
---
```

首次同步后，插件会自动在 frontmatter 中添加 `NotionID-{abName}` 字段，用于追踪关联的 Notion 页面。

---

## 开发 / Development

```bash
# 安装依赖 / Install dependencies
npm install

# 构建 / Build
npm run build

# 输出文件为 main.js / The output is main.js
```

---

## 致谢 / Credits

- [EasyChris/obsidian-to-notion](https://github.com/EasyChris/obsidian-to-notion) — 原版插件 / original plugin
- [jxpeng98/obsidian-to-NotionNext](https://github.com/jxpeng98/obsidian-to-NotionNext) — 功能增强和 NotionNext 支持 / feature enhancements and NotionNext support

---

## 许可证 / License

MIT
