---
name: zotero-fulltext-archiver
description: "将已有 Zotero PDF 或历史 MinerU 输出归档为可追踪的 ResearchVault Fulltext Markdown：先复用已成功的 MinerU 链路，再写入统一 frontmatter、整理安全图片路径、保留页面映射并执行只读校验。此技能不做中文总结、不改写论文正文、不负责用户检索。"
---

# Zotero Fulltext Archiver。

## Local macOS configuration

Before filesystem or MinerU work, read `../../LOCAL_CONFIG.md`. Use the
configured Obsidian paths and repository templates; never use historical
Windows runner paths.

Resolve repository-relative templates from the directory containing
`LOCAL_CONFIG.md`. Before a successful archive write, create
`$FULLTEXT_DIR/<collection>/` and its `images/<zotero_key>/` subdirectory as
needed; do not create them during a read-only existence check.

## Production safeguards (macOS / cloud or local MinerU)

When a validated Zotero PDF must be run through MinerU, use an ASCII-only
*working copy* in `$MINERU_STAGING_DIR`. The Zotero attachment remains
read-only. Record the source and working-copy SHA-256 values and do not invoke
MinerU until they match.

Prefer the Fulltext cache already produced by llm-for-zotero. The cloud MinerU
credential is managed outside this public workflow by the plugin; never place
it in a command, file, log, or frontmatter. If a new local conversion is
explicitly needed, use a locally detected `mineru`, `python3 -m mineru`, or
`mineru-api` route. Give a complete article a hard limit of at least 60 minutes.
A no-progress stop may be used only after 10--15 minutes during which stdout,
stderr, output files, and process CPU time have all remained inactive. Abort
for persistently available RAM below 2 GB only after a sustained observation,
and record the resource samples.

Every invocation must stream timestamped stdout and stderr to the run
directory, record stage transitions, and clean only the MinerU CLI tree and
new `mineru.cli.fast_api` descendants created by that invocation. Recheck for
those exact processes after cleanup; never terminate unrelated Python work.

Archive only after the raw Markdown gate passes: non-empty full-document
front/middle/end samples, source identity, image-file/reference checks, and
no missing image targets. Formal archiving may add schema frontmatter and
rewrite image paths, but must otherwise preserve the extracted body verbatim.

Before invoking MinerU, inspect the formal `03fulltext` path by `zotero_key`.
If a formal Fulltext already has matching `zotero_key`/`pdf_key`, valid
frontmatter, resolved images, and a non-empty article body, reuse it and run
targeted validation. A Note-template repair is not a reason to rerun MinerU.
If `page_mapping` is `unknown`, retain that value; the Analytical Writer may
still verify quotation pages directly against the read-only Original PDF.

## 职责边界

执行：`Zotero PDF → MinerU → Fulltext Markdown → 图片整理 → metadata → Note 关联 → validation`。

不执行中文翻译、分析笔记写作、批量检索或 Zotero 数据库改写。

## 1. 先确认实际 MinerU 环境

不要重新安装 MinerU。先检查 llm-for-zotero 是否已有可复用的 Fulltext
缓存；只有在明确需要新转换时，才探测 `command -v mineru`、
`python3 -m mineru` 和 `mineru-api`。任何中间输出都写入
`$MINERU_STAGING_DIR`，不写入 Obsidian Vault。

如果既没有云端 API，也没有可用的本地 MinerU，停止全文阶段并报告
`FULLTEXT_DEFERRED`，不要用普通 PDF 文本抽取结果冒充正式 MinerU Fulltext。

## 2. 归档路径

正式全文：

```text
$FULLTEXT_DIR/<collection>/<zotero_key>.md
$FULLTEXT_DIR/<collection>/images/<zotero_key>/<image-file>
```

历史 MinerU 批处理结果如果存在，只能作为外部暂存输入；不作为默认正式全文检索目录。当前 Vault 的分析笔记位于 `Zotero Notes/` 时，不移动它们；仅在全文 frontmatter 中写准确的 `note_path`。

## 3. 优先迁移旧结果

若 `MinerU_batch` 已有与 `zotero_key` 唯一对应的 Markdown 和图片：

1. 确认 Zotero 主键、PDF 键、标题和 Collection。
2. 将旧 Markdown 复制到正式 `$FULLTEXT_DIR/<collection>/<zotero_key>.md`；分析笔记中的 Obsidian 链接使用 `03fulltext/<collection>/<zotero_key>`。
3. 将图片复制到 `images/<zotero_key>/`，不得使用完整论文标题作为目录名。
4. 将原有图片引用改为相对于 Fulltext Markdown 的安全路径，例如 `![](<images/Q22PFLNV/image.jpg>)`。
5. 逐一检查每个本地图片引用真实存在；有缺失时不能报告成功。

若没有可复用结果，才调用已确认的 MinerU 可执行文件处理单篇 PDF；不得批量重跑整个库。

## 4. Fulltext Frontmatter

每个正式全文顶部至少包含：

```yaml
---
type: literature-fulltext
title: "..."
zotero_key: "Q22PFLNV"
pdf_key: "4RMSR7ZR"
doi: "..."
collection: "创新经济地理"
note_path: "Zotero Notes/创新经济地理/论文标题.md"
fulltext_path: "03fulltext/创新经济地理/Q22PFLNV.md"
zotero_item: "zotero://select/library/items/Q22PFLNV"
zotero_pdf: "zotero://open-pdf/library/items/4RMSR7ZR"
source_type: mineru
page_mapping: unknown
---
```

Vault 内部路径统一使用 `/`。缺失的 DOI 可留空，但不得伪造。

## 5. 原文与页码规则

- MinerU Markdown 是证据档案：不翻译、总结、润色、重写、删减或插入模型生成内容。
- 允许的后处理仅包括 frontmatter、机器定位标记和安全图片路径修复。
- 只有当 `content_list.json`/`middle.json` 等信息与真实 PDF 通过单篇测试可靠对应时，才写 `page_mapping: reliable` 或 `<!-- pdf_page: N -->`。
- 0-based/1-based 转换必须记录并用真实 PDF 验证；无法可靠映射时写 `page_mapping: unknown`，不要猜 page。

## 6. 关联与校验

归档完成后：

1. 分析笔记补 `fulltext_path`，并可增加 `[[03fulltext/<collection>/<zotero_key>]]` 入口；不因全文归档重写整篇笔记，模板化重排由 `zotero-analytical-writer` 单独负责。
2. Fulltext 补 `note_path`，确认双方 `zotero_key`、`pdf_key` 一致。
3. 若仓库或用户提供了文献链接校验脚本，运行该脚本；本仓库当前没有内置 validator 时，执行本节的人工检查并报告 `MANUAL_VALIDATION`，不假装运行不存在的脚本。
4. 只有 PDF、Fulltext、图片、Note、链接均有效时，才向 Collection Manager 报告 COMPLETE。
