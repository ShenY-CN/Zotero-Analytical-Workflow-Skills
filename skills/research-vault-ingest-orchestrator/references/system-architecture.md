# ResearchVault production architecture

```text
Zotero parent item (zotero_key)
  + PDF attachment (pdf_key)
        |
        v
$FULLTEXT_DIR/<collection>/<zotero_key>.md
  - original-language MinerU archive
  - image assets and reciprocal note link
        |
        v
$ANALYTICAL_NOTES_DIR/<collection>/<paper>.md
  - Chinese structured single-paper Analytical Note
  - Zotero identity and fulltext_path
        |
        v
$KNOWLEDGE_DIR/
  - Chinese human-facing derived synthesis
$KNOWLEDGE_META_DIR/
  - claims, gaps, indexes and machine traceability
```

`$KNOWLEDGE_DIR` is a navigation and synthesis layer, not primary scientific evidence. Evidence must remain traceable as Knowledge → Analytical Note → Fulltext → optional original PDF.

| Layer | Responsibility | Stable identity |
| --- | --- | --- |
| Zotero | Parent metadata and attached source PDF | `zotero_key`, `pdf_key` |
| `$FULLTEXT_DIR` | Verbatim source archive and images | `zotero_key`, `pdf_key` |
| `$ANALYTICAL_NOTES_DIR` | Structured single-paper interpretation | `zotero_key` |
| `$KNOWLEDGE_DIR` | Cross-paper Chinese synthesis and navigation | source Note links |
| `$KNOWLEDGE_META_DIR` | Claims, gaps and machine-only traceability | claim/gap IDs plus Note-derived identity |
