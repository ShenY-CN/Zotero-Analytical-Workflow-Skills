# Zotero Analytical Workflow Skills

本仓库的 Obsidian 目录和模板位置统一定义在
[`LOCAL_CONFIG.md`](LOCAL_CONFIG.md)。任何执行型 Skill 都必须先读取该文件；本文不再把历史 Windows 路径作为运行配置。
工作流根目录通过 `LOCAL_CONFIG.md` 的实际位置动态解析，因此整个仓库可以移动。

这不是单个“论文精读 skill”，而是一整条 Zotero 文献处理工作流的打包仓库。

仓库当前包含 7 个核心 skill 和 7 个模板文件，用来覆盖：

- 论文分类批处理与断点续跑
- 论文元数据、批注、全文缓存提取
- PDF/MinerU 全文归档与可追踪链接维护
- 中文精读笔记生成与模板套用
- 研究知识库的模板化维护与跨论文综合
- ResearchVault 内的 Note-first 文献检索与原文核验
- Zotero、Fulltext、精读笔记和 Knowledge 的端到端编排

## 目录结构

```text
Zotero-analytical-writer/
├── README.md
├── skills/
│   ├── zotero-collection-manager/
│   │   └── SKILL.md
│   ├── zotero-data-fetcher/
│   │   └── SKILL.md
│   ├── zotero-fulltext-archiver/
│   │   └── SKILL.md
│   ├── zotero-analytical-writer/
│   │   └── SKILL.md
│   ├── research-vault-knowledge-maintainer/
│   │   └── SKILL.md
│   ├── research-vault-ingest-orchestrator/
│       └── SKILL.md
│   └── research-vault-literature-retrieval/
│       └── SKILL.md
├── templates/
│   ├── 论文精读模板.md
│   └── 知识库模板/
│       ├── README_知识库模板说明.md
│       ├── 主题模板.md
│       ├── 概念模板.md
│       ├── 方法模板.md
│       ├── 关系模板.md
│       └── 争议模板.md
└── zotero_obsidian/
    └── zotero-analytical-workflow-entry.md  # llm-for-zotero 入口 Skill
```

## llm-for-zotero 入口

`llm-for-zotero` 不会递归识别本仓库的 `skills/*/SKILL.md`。请将
[`zotero_obsidian/zotero-analytical-workflow-entry.md`](zotero_obsidian/zotero-analytical-workflow-entry.md)
复制到插件的顶层 Skill 目录：

```text
{ZoteroDataDir}/llm-for-zotero/skills/zotero-analytical-workflow-entry.md
```

当前机器通常是：

```text
/Users/sheny/Zotero/llm-for-zotero/skills/
```

只有这个入口文件需要放进 `llm-for-zotero/skills/`；完整工作流仓库可以放在
任意位置。入口会通过 `workflow_root`、`ZAW_WORKFLOW_ROOT` 或 marker 文件
定位工作流根目录。复制后重启插件，或在 Skills 页面新建/删除一次 Skill 触发重新扫描。

使用时建议显式写 `ZAW`，例如：

```text
ZAW：对当前论文执行完整入库。zotero_key=XXXXXXX
```

## 工作流关系

推荐按下面顺序使用：

1. `zotero-collection-manager`
   负责读取某个 Zotero 分类、比对处理日志、筛出未完成文献并串行调度。
2. `zotero-data-fetcher`
   负责抓取单篇论文的元数据、批注、全文缓存和附件信息。
3. `zotero-fulltext-archiver`
   负责归档已处理的 PDF/MinerU 全文，整理图片和元数据，并维护 Note 与 Fulltext 的双向关联。
4. `zotero-analytical-writer`
   负责中文逻辑重构、Frontmatter 提炼、模板套用和 Obsidian 笔记写入。
5. `research-vault-ingest-orchestrator`
   负责按单篇论文编排 Zotero 身份、Fulltext、精读笔记、Knowledge 决策和校验。
6. `research-vault-knowledge-maintainer`
   负责按知识库模板维护主题、概念、方法、关系、争议和综合页，并执行跨论文覆盖与证据审查。
7. `research-vault-literature-retrieval`
   处理 ResearchVault 文献问题时，先从 Analytical Notes 定位论文，再按需进入对应 Fulltext 或 Zotero PDF 核验。

其中：

- `templates/论文精读模板.md` 是精读模板
- `templates/知识库模板/` 包含主题、概念、方法、关系、争议及使用说明模板

## 仓库内容说明

### `skills/zotero-collection-manager`

适用于整批处理 Zotero 分类。它强调：

- 读取并维护 `_ProcessLog_进度记录.md`
- 自动跳过已成功或已跳过条目
- 按篇串行执行，处理完一篇立即写入日志
- 将抓取与写作拆给下游 skill

### `skills/zotero-data-fetcher`

适用于单篇论文语料准备。它强调：

- 先读 Zotero 数据目录和数据库
- 优先取批注，其次取全文缓存，再考虑本地 PDF
- 保持原始语言，不在此步骤翻译或总结

### `skills/zotero-fulltext-archiver`

用于将 Zotero PDF 或已有 MinerU 结果归档为可追踪的 Fulltext Markdown。它负责保留 stable key、图片路径、前置元数据以及与 Analytical Note 的双向关联。

### `skills/zotero-analytical-writer`

适用于最终精读笔记生成。它强调：

- Frontmatter 字段必须高度提炼，不能机械复制摘要
- 研究区、数据来源、方法、核心变量要精准提取
- 公式提取要防乱码、防胡编，并支持 OCR 兜底
- 正文区要过滤作者单位、基金号、投稿规范等学术噪音

### `skills/research-vault-ingest-orchestrator`

适用于指定论文的端到端入库。它强调：

- 先确认 Zotero 稳定身份，再复用或归档可追踪的 Fulltext
- Analytical Note 与原文证据分层处理，精确结论、公式、阈值和引语必须回到原文核验
- Knowledge 写入前必须读取当前知识库模板，并建立逐篇覆盖账本
- 以可重试状态、重复检查和最终校验作为完成条件

### `skills/research-vault-knowledge-maintainer`

适用于从已有精读笔记和 Fulltext 维护 Research Knowledge Wiki。它强调：

- 严格使用 [`templates/知识库模板/`](templates/知识库模板/) 的 README 和对应页面模板
- 区分结构化库字段与原文证据，保留真实 `source_notes`、证据表、边界、缺口和争议
- “全部论文”任务必须建立逐篇 coverage ledger，不能只生成综合页
- 精确结论、公式、阈值、机制和引语需要 Fulltext 支持；缺全文时明确标注延后核验

### `skills/research-vault-literature-retrieval`

用于回答基于当前 ResearchVault 的文献问题。它以 Analytical Notes 为默认检索入口，以相应 Fulltext 或 Zotero PDF 做定向补充与核验，避免无边界扫描全文库。

## 使用建议

- 如果你是把这些 skill 用于 Codex 或类似代理系统，建议保持当前目录结构不变。
- `zotero-analytical-writer` 使用 [`templates/论文精读模板.md`](templates/论文精读模板.md)；知识库维护 skill 使用 [`templates/知识库模板/`](templates/知识库模板/)。

## 环境说明

本工作流不是 macOS 专属版本。平台相关配置统一放在 [`LOCAL_CONFIG.md`](LOCAL_CONFIG.md)：

- 用户只需设置 `OBSIDIAN_VAULT_ROOT`；当前仓库已填入一个可直接使用的本机路径示例。
- `Zotero Notes/`、`03fulltext/` 和 `01knowledge/` 都是相对于 Vault 的目录。
- 进入对应写入阶段时，缺少的目标目录会自动创建。
- 模板始终相对于可移动的 `WORKFLOW_ROOT` 解析。
- MinerU 不绑定某个操作系统的可执行文件；优先复用 llm-for-zotero 结果，必要时运行时探测可用后端。

## 后续可继续补充

- 增加示例输入与输出
- 增加安装说明或依赖说明
- 为每个 skill 单独补充测试样例或演示数据
