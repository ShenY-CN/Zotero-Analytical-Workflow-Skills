---
id: zotero-analytical-workflow-entry
description: Route explicit Zotero Analytical Workflow requests into the multi-stage ResearchVault workflow. Use for complete paper ingest, collection processing, fulltext archiving, analytical notes, Knowledge updates, or project-file retrieval; not for ordinary paper Q&A.
version: 1
contexts: any
activation: auto
match: /\bZAW\b/i
match: /Zotero[- ]Analytical[- ]Workflow/i
match: /ResearchVault.*(完整|端到端|入库|工作流)/i
match: /(全文归档|分析笔记|知识库维护|Collection.*增量).*(完整|工作流|入库)/i
---

# Zotero Analytical Workflow entrypoint

This is only a router. It does not replace the specialist Skills in the
`Zotero-Analytical-Workflow-Skills` repository and it must not invent a shorter
summary workflow.

## Resolve the workflow repository

Before doing any filesystem work, locate the directory that contains both:

- `LOCAL_CONFIG.md`
- `skills/research-vault-ingest-orchestrator/SKILL.md`

Resolve it in this order:

1. Use a `workflow_root=...` path explicitly supplied in the user's message.
2. Use the `ZAW_WORKFLOW_ROOT` environment variable when available.
3. Check the current working directory and its parents, then search the user's
   home directory for the two marker files. Do not assume the original path of
   this repository and do not scan unrelated system directories.
4. If zero or multiple candidates remain, stop and ask the user for the
   workflow root instead of guessing.

After resolving the root, read `LOCAL_CONFIG.md` first. Then read only the
specialist Skill and references needed for the selected mode. The repository
root is the directory containing `LOCAL_CONFIG.md`; it may be moved without
changing this entry file.

## Route by request

- A specified paper's complete ingest or status assessment:
  `skills/research-vault-ingest-orchestrator/SKILL.md`
- A Zotero Collection or small batch:
  `skills/zotero-collection-manager/SKILL.md`
- Zotero item metadata, annotations, cached fulltext, or PDF identity:
  `skills/zotero-data-fetcher/SKILL.md`
- PDF/MinerU Fulltext archiving:
  `skills/zotero-fulltext-archiver/SKILL.md`
- A Chinese Analytical Note:
  `skills/zotero-analytical-writer/SKILL.md`
- Cross-paper Knowledge pages, claims, relations, controversies, or synthesis:
  `skills/research-vault-knowledge-maintainer/SKILL.md`
- Questions grounded in the existing Obsidian project:
  `skills/research-vault-literature-retrieval/SKILL.md`

For a complete specified-paper ingest, follow this order and stop at a failed
gate:

```text
state inspection
→ Zotero identity
→ Fulltext gate
→ Analytical Note gate
→ Note/Fulltext validation
→ Knowledge decision
→ Knowledge update or NO_KNOWLEDGE_CHANGE
→ final validation
→ append-only log
```

Read the orchestrator's `references/` files for gate details, pipeline
contracts, workflow states, and validation. Do not invoke all specialist Skills
blindly; select the minimum stages required by the observed state.

## Evidence and write rules

- Use `zotero_key` as the primary paper identity and `pdf_key` for the PDF.
- Use the `ANALYTICAL_NOTES_DIR`, `FULLTEXT_DIR`, and `KNOWLEDGE_DIR` resolved
  from `LOCAL_CONFIG.md` for their respective layers. Create missing destination
  directories only immediately before a write; read-only inspection must not
  create empty directories.
- Reuse valid llm-for-zotero Fulltext/cache results before attempting a new
  MinerU conversion. MinerU credentials are managed outside this public
  workflow by llm-for-zotero; never request, print, store, or pass a secret from
  this Skill.
- Keep Fulltext as original-language evidence. Do not generate quotations,
  page numbers, formulas, or `fulltext_verified` claims from a Note alone.
- Before any Zotero, Markdown, Fulltext, Knowledge, or log write, present the
  planned changes and wait for the plugin's approval flow.
- For Collection or “all papers” tasks, build the required coverage ledger and
  report unresolved papers; do not treat a combined synthesis page as complete
  coverage.

## Invocation examples

Use an explicit prefix so this entrypoint is selected instead of the plugin's
generic note or summary Skills:

```text
ZAW：对当前论文执行完整入库。
```

```text
ZAW：增量处理当前 Zotero Collection，完成全文归档、分析笔记和知识库覆盖检查。
```

```text
ZAW workflow_root=/path/to/Zotero-Analytical-Workflow-Skills：
基于当前 Obsidian 项目文件检索这个研究问题。
```
