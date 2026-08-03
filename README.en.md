# Second Brain Starter

[한국어](README.md) | **English**

**A personal knowledge wiki operated *by* an LLM, not just written *with* one — built on Claude Code, structured as an ontology.**

Based on [Karpathy's LLM Wiki idea](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f): you drop raw material in, the LLM reads it through *your* lens, writes and cross-links wiki pages, and answers questions with page-level citations. This starter adds an **ontology layer** (typed entities, typed relations, live data bindings) so the wiki stays queryable and healthy as it grows.

> This is the generalized template of a wiki I operate daily (90+ pages). Names and content here are fictional samples; the operating rules and schema are the real ones.

## How it works

```
raw/   ← you drop source material here (LLM never edits this)
wiki/  ← LLM writes/updates pages here, cross-linked with [[wikilinks]]
CLAUDE.md  ← the operating rules the LLM follows (the heart of this repo)
```

Four operations, all triggered by plain Korean/English chat:

| Operation | Trigger | What happens |
|---|---|---|
| **Ingest** | "이거 정리해줘" | Reads raw/, extracts takeaways through your personal lens, writes/updates a wiki page, cross-links it, updates the index and log |
| **Ask** | any question | Finds relevant pages via the index, answers **with page citations**, flags gaps |
| **Health check** | "건강검진" | Orphan pages, broken links, duplicates, stale pages, index drift, missing takeaways, ontology violations |
| **Standup** | "standup" | Pulls your live sources (calendar, saved messages, docs) by SoT priority and briefs today's actions — answer-only, never auto-edits the wiki |

## The ontology layer

Plain wikis rot: pages pile up, links break, nothing is queryable. This starter treats the wiki as an ontology with three layers (inspired by Palantir's model):

- **Semantic** — every page declares a `type:` (Project, Method, Case, Policy, …) from a controlled vocabulary in [`wiki/_ontology.md`](wiki/_ontology.md); meaningful links carry typed relations (`part_of`, `impacts`, `evaluates`, …) aggregated in [`wiki/_entities.md`](wiki/_entities.md)
- **Kinetic** — living pages declare data bindings (`src_jira`, `src_calendar`, …) so questions get answered with *current* state, pulled at answer time
- **Dynamic** — decisions are version-controlled like code: AI proposal = branch, your approval = merge, history = log

Two rules keep it honest: new types/relations must be registered in the schema **before** use, and pulled live data never silently overwrites the wiki body.

## Quickstart

```bash
git clone <this repo> my-second-brain
cd my-second-brain
claude
```

1. Open `CLAUDE.md` and fill in **§0 (your context)** — role, mission, domains, career direction. Every takeaway the LLM writes is filtered through this lens, so this section does the most work.
2. Drop any document into `raw/` and say **"정리해줘"**. Check the sample pages in `wiki/` to see the expected output shape.
3. Ask it something. Then run **"건강검진"** after a week.

## What to customize

| Where | What |
|---|---|
| `CLAUDE.md` §0 | Your context table + the two takeaway lenses (default: 실무 적용 / 커리어 자산) |
| `wiki/_ontology.md` §1–2 | Entity types and relations — start with the defaults, register new ones as needed |
| `wiki/_ontology.md` §4 | Your SoT priority — which source do you trust first for "my tasks"? |
| Sample pages | Delete them once you have real ones |

## Design notes

- **Flat folders, category by index.** No folder taxonomy debates; `wiki/index.md` is the single navigation surface.
- **Takeaway-first pages.** Every page opens with a callout answering "so what does this mean for *me*" in two lenses, before any content.
- **Update over create.** Same topic → update the existing page. The log records every operation.
- **Facts vs. inference are marked.** What the source says vs. what the LLM concluded stay distinguishable.
- **Growth valve.** When the wiki approaches ~150 pages, the health check flags it and the schema points to an embedding/RAG upgrade path with ontology-metadata filters.
