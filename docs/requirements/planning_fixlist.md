# Project Aster – Planning Docs Patch List (Per File)

> Use this as a line‑item checklist for the Cursor bulk edit + quick manual passes. Each item is small and surgical.

## 1) `docs/planning/master-plan.md`

### Must-do edits
- **Add a 5‑bullet glossary** near the top (after Project Overview):
  - *Watcher*: fetch new papers/metadata (arXiv/S2).
  - *Indexer*: chunk + embed abstracts, store vectors (pgvector).
  - *Digestor*: produce structured summaries.
  - *Question Miner*: extract open questions (+ evidence quotes).
  - *Bloom*: web UI for search & paper detail.
- **Normalize event names** everywhere to **canonical strings**:
  - `paper.found`, `paper.indexed`, `paper.summarized`, `paper.questions_mined`, `graph.updated`, `user.feedback`.
- **Jobs API: add idempotency note** in the Jobs section:
  - “Job creation supports **Idempotency‑Key** to dedupe repeated requests.”
- **Cross‑reference new docs** in an early “Related Specs” line:
  - `See: jobs_api.md · events_and_data_contracts.md`
- **Metrics alignment** (footer of MVP Metrics section):
  - “Module metrics inherit/align with these; see each module for local adds.”

### Nice-to-have
- Verify “No Kafka/Neo4j/Elasticsearch/TUI/Mobile in MVP” appears once in **MVP Constraints** and not duplicated elsewhere.


## 2) `docs/planning/modules/aster_bloom.md`

### Must-do edits
- **Integration Points → Input Dependencies**: explicitly name the miner
  - “Consumes `paper.questions_mined` from **aster_question_miner**.”
- **MVP Features → 1. Content Discovery & Browsing**:
  - Add one explicit line: “MVP UI surface is **Web (Next.js)**; **CLI optional** for dev; **TUI postponed** (future phase).”
- **Top of file – add “Related Specs” block** beneath Module Overview:
  - `Related: jobs_api.md · events_and_data_contracts.md`
- **Event name casing**: ensure all outgoing/consumed events use canonical names above.
- **Metrics section footer**:
  - “Aligned to master-plan.md → MVP Metrics.”

### Nice-to-have
- Add a one‑sentence note under “System Design” clarifying **sync vs async**:
  - “Synchronous search over existing corpus; async corpus refresh/distill via Jobs API.”


## 3) `docs/planning/modules/aster_watcher.md`

### Must-do edits
- **MVP Constraints**: add explicit sentence on **sync vs async**:
  - “Interactive search uses existing corpus (sync); new discovery/refresh runs via **Jobs API** (async).”
- **Top of file – add “Related Specs” block**:
  - `Related: jobs_api.md · events_and_data_contracts.md`
- **Event names**: normalize to canonical names (see master plan).
- **Jobs API note** where job creation is described:
  - “Accepts **Idempotency‑Key** to dedupe.”

### Nice-to-have
- Under “Risks & Mitigations”, add:
  - *Rate limits*: backoff + API quotas.
  - *Source ToS*: respect robots.txt / official APIs.


## 4) `docs/planning/modules/aster_indexer.md`

### Must-do edits
- **MVP Constraints**: add **sync vs async** sentence (same as Watcher).
- **Top of file – add “Related Specs” block**:
  - `Related: jobs_api.md · events_and_data_contracts.md`
- **Event names**: use `paper.indexed` (canonical), etc.
- **Metrics footer**: “Aligned to master-plan.md → MVP Metrics.”

### Nice-to-have
- In “Semantic Indexing”, confirm **pgvector** is noted as the MVP store.


## 5) `docs/planning/modules/aster_digestor.md`

### Must-do edits
- **Top of file – add “Related Specs” block**:
  - `Related: jobs_api.md · events_and_data_contracts.md`
- **Event names**: ensure `paper.summarized` is used.
- **MVP Constraints**: note abstracts-first; light PDF sections optional (Future Work/Limitations).
- **Metrics footer**: “Aligned to master-plan.md → MVP Metrics.”

### Nice-to-have
- In Testing, add a line for **structured output validation** (JSON schema for summaries).


## 6) `docs/planning/modules/aster_question_miner.md`

### Must-do edits
- **Top of file – add “Related Specs” block**:
  - `Related: jobs_api.md · events_and_data_contracts.md`
- **Event names**: ensure `paper.questions_mined` is the emitted event.
- **MVP Constraints**: spell out **two inputs**:
  - (a) abstract‑only path; (b) optional light PDF section (Future Work/Limitations).
- **Prompt contract**: add a short JSON schema in the doc for LLM output:
  - ```json
    { "question": "...",
      "evidence_text": "...",
      "confidence": 0.0 }
    ```

### Nice-to-have
- Risks: *hallucination → mitigate via evidence requirement & refusal when evidence missing*.


## 7) `docs/planning/modules/aster_ranker.md`

### Must-do edits
- **Top of file – add “Related Specs” block**:
  - `Related: jobs_api.md · events_and_data_contracts.md`
- **MVP method clarity**:
  - “Ranking = recency prior + cosine similarity to query/user topic; personalization minimal in MVP.”
- **Event names**: if consuming feedback, use `user.feedback` (canonical).
- **Metrics footer**: “Aligned to master-plan.md → MVP Metrics.”

### Nice-to-have
- Add tiny “Why recommended” signals list (score components) for Bloom integration.


---

## Global consistency pass (run once after per-file edits)

- **Canonical events** only: `paper.found`, `paper.indexed`, `paper.summarized`, `paper.questions_mined`, `graph.updated`, `user.feedback`.
- **Jobs API** mentions include **Idempotency‑Key** guidance.
- **Sync vs async** wording appears in Watcher, Indexer, and Bloom.
- **Related Specs** block present at top of *each* module.
- **Metrics** sections point back to master-plan MVP metrics.
- **MVP constraints** everywhere forbid Mobile/TUI parity/Elasticsearch/Neo4j/Kafka in v0.
- Confirm **Postgres + pgvector** appear as MVP defaults in relevant modules.
