# 通用路径配置：Zotero Analytical Workflow

本文件是唯一的路径管理和路径解释文档。执行任何需要读写文件的阶段前，先读取本文件。

## 1. 用户需要设置的路径（只修改这一段）

下面的值可以直接用于当前机器。迁移到另一台电脑时，只需要修改
`OBSIDIAN_VAULT_ROOT`；工作流文件夹本身不需要写死路径。

```text
# 当前机器可直接使用；换机器时改成对应 Obsidian Vault 根目录。
OBSIDIAN_VAULT_ROOT = /Users/sheny/Documents/Obsidian Vault

# 相对于 OBSIDIAN_VAULT_ROOT 的目录名。通常不需要修改。
ANALYTICAL_NOTES_RELATIVE = Zotero Notes
FULLTEXT_RELATIVE = 03fulltext
KNOWLEDGE_RELATIVE = 01knowledge
```

如果不希望把个人路径提交到公开仓库，可以将上面的
`OBSIDIAN_VAULT_ROOT` 留空，并通过运行环境变量或用户提示提供：

```text
OBSIDIAN_VAULT_ROOT = <你的 Obsidian Vault 根目录>
```

推荐的外部变量名是 `OBSIDIAN_VAULT_ROOT`。如果路径为空，入口 Skill 应先寻找包含
`.obsidian/` 的 Vault；找到多个候选时必须询问用户，不能猜测。

## 2. 自动解析的路径（不要修改）

```text
# 工作流根目录：本文件所在目录。整个工作流文件夹可以移动。
WORKFLOW_ROOT = directory containing LOCAL_CONFIG.md

ANALYTICAL_NOTES_DIR = OBSIDIAN_VAULT_ROOT/ANALYTICAL_NOTES_RELATIVE
FULLTEXT_DIR = OBSIDIAN_VAULT_ROOT/FULLTEXT_RELATIVE
KNOWLEDGE_DIR = OBSIDIAN_VAULT_ROOT/KNOWLEDGE_RELATIVE
KNOWLEDGE_META_DIR = KNOWLEDGE_DIR/.meta

ANALYTICAL_NOTES_LINK_ROOT = ANALYTICAL_NOTES_RELATIVE
FULLTEXT_LINK_ROOT = FULLTEXT_RELATIVE
KNOWLEDGE_LINK_ROOT = KNOWLEDGE_RELATIVE

ANALYTICAL_NOTE_TEMPLATE = WORKFLOW_ROOT/templates/论文精读模板.md
KNOWLEDGE_TEMPLATE_DIR = WORKFLOW_ROOT/templates/知识库模板

# 云端 MinerU 由 llm-for-zotero 管理；公开工作流不保存、不读取、不传递密钥。
MINERU_PROVIDER = llm-for-zotero-managed
MINERU_RUNNER = runtime-discovered
MINERU_STAGING_DIR = system temporary directory / zaw-mineru
```

## 3. 通用运行规则

- 所有 Obsidian 内部链接使用 `/` 和相对 Vault 根目录的路径；不能把本机绝对路径写进 Markdown 链接。
- `ANALYTICAL_NOTES_DIR`、`FULLTEXT_DIR`、`KNOWLEDGE_DIR` 和 `KNOWLEDGE_META_DIR` 不存在时，在对应写入阶段自动创建；只读检查阶段不创建空目录。
- 模板始终从 `WORKFLOW_ROOT/templates/` 读取，不复制到 Obsidian Vault。
- MinerU 只处理 Zotero PDF 的只读副本。运行前记录源文件和工作副本的 SHA-256，使用系统临时目录暂存，不把中间文件写入 Vault。
- MinerU API 密钥属于外部秘密配置：不得写入 Markdown、Skill、Git、shell 命令历史或日志。由 llm-for-zotero 的设置管理；本工作流只读取已经生成的结果。
- 本仓库当前不包含固定的 Knowledge validator 或 literature-link validator。若后续加入脚本，先确认其位置；否则执行 Skill 中列出的人工校验，并报告 `MANUAL_VALIDATION`。
- Zotero 数据库、PDF 附件和 Obsidian 中已有笔记都不得被批量移动或重命名，除非用户明确要求。

## 4. 当前机器的有效配置

当前仓库已经将 `OBSIDIAN_VAULT_ROOT` 设置为：

```text
/Users/sheny/Documents/Obsidian Vault
```

因此本机可以直接使用；公开发布给其他用户时，只需修改“第 1 节”。
