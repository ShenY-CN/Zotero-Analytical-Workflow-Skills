# 本机配置：macOS + Obsidian

本文件是本工作流在当前 Mac 上的路径配置。执行任何需要读写文件的阶段前，先读取本文件；不要再使用历史文档中的 Windows 绝对路径。

## Canonical paths

```text
# Resolve this dynamically as the directory containing LOCAL_CONFIG.md.
# Do not replace it with an absolute path: the whole workflow folder is movable.
WORKFLOW_ROOT = directory containing this file
OBSIDIAN_VAULT = /Users/sheny/Documents/Obsidian Vault

# 现有的 Obsidian 文献笔记目录；不要为了迁移工作流重命名或移动它。
ANALYTICAL_NOTES_DIR = /Users/sheny/Documents/Obsidian Vault/Zotero Notes
ANALYTICAL_NOTES_LINK_ROOT = Zotero Notes

# 以下目录在进入对应写入阶段时自动创建；只读检查阶段不创建目录。
FULLTEXT_DIR = /Users/sheny/Documents/Obsidian Vault/03fulltext
FULLTEXT_LINK_ROOT = 03fulltext
KNOWLEDGE_DIR = /Users/sheny/Documents/Obsidian Vault/01knowledge
KNOWLEDGE_META_DIR = /Users/sheny/Documents/Obsidian Vault/01knowledge/.meta
KNOWLEDGE_LINK_ROOT = 01knowledge

# 模板以 WORKFLOW_ROOT 下的版本为唯一结构权威，不复制到 Obsidian Vault。
ANALYTICAL_NOTE_TEMPLATE = WORKFLOW_ROOT/templates/论文精读模板.md
KNOWLEDGE_TEMPLATE_DIR = WORKFLOW_ROOT/templates/知识库模板

# 云端 MinerU 由 llm-for-zotero 管理。公开工作流不保存、不读取、不传递密钥。
MINERU_PROVIDER = llm-for-zotero-managed
# 若不使用插件托管的缓存，才在运行时探测 command -v mineru、python3 -m mineru
# 或用户配置的 mineru-api；找不到时将 Fulltext 标记为 deferred。
MINERU_RUNNER = runtime-discovered
MINERU_STAGING_DIR = /private/tmp/zaw-mineru
```

## macOS 运行规则

- 所有 Obsidian 内部链接使用 `/`，并使用 `ANALYTICAL_NOTES_LINK_ROOT`、`FULLTEXT_LINK_ROOT` 和 `KNOWLEDGE_LINK_ROOT`，不能把本机绝对路径写进 Markdown 链接。
- `Zotero Notes` 是当前 Analytical Note 层；历史文档中的 `02vault` 只是旧的概念名称，不是本机目录。
- `Zotero Notes`、`03fulltext`、`01knowledge` 及 `01knowledge/.meta` 不存在时，在对应写入阶段自动创建；不要为了只读检查而创建空目录。
- MinerU 只处理 Zotero PDF 的只读副本。运行前记录源文件和工作副本的 SHA-256；使用 `/private/tmp/zaw-mineru` 做暂存，不把中间文件写入 Vault。
- MinerU API 密钥属于外部秘密配置：不得写入 Markdown、Skill、Git、shell 命令历史或日志。由 llm-for-zotero 的 MinerU 设置管理；本工作流只读取已经生成的结果。
- 本仓库当前不包含固定的 Knowledge validator 或 literature-link validator。若后续加入脚本，先确认其位置；否则执行 Skill 中列出的人工校验，并报告 `MANUAL_VALIDATION`，不能假装运行了不存在的脚本。
- Zotero 数据库、PDF 附件和 Obsidian 中已有笔记都不得被批量移动或重命名，除非用户明确要求。

## 本地模板

- 单篇论文模板：`templates/论文精读模板.md`
- 知识库模板：`templates/知识库模板/`

这些模板已经在工作流仓库中；创建或修复笔记时直接读取这里的文件。
