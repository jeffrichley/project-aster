# Project Aster – Planning Docs Update Brief

**Scope**: All docs under `docs/planning` and the `aster_bloom.md` spec.

## Global changes
- Unify on the **MVP-first approach**: a single monolith/modulith with clean internal modules, Postgres + pgvector, no Kafka/Neo4j/Elasticsearch in v0.
- Replace vague "first phase" descriptions with the **slim workflow**:

  watcher (arXiv/S2) → indexer (abstracts only, pgvector) → digestor (structured summary from abstract) → open-question miner (abstract or light PDF section) → ranker (simple recency + similarity) → bloom (web UI search & detail).

- No mobile app, no heavy PDF parsing, no real-time collaboration, no multi-interface parity in MVP. These move to future phases.
- All module planning docs should:
  - Mark MVP features vs. future features explicitly.
  - In MVP, only abstract metadata is ingested; PDFs are optional and light-parsed for “Future Work/Limitations” sections if used.
  - Open-question miner may use heuristics + LLM on abstracts or light PDF text, returning questions with evidence quotes.
  - Bloom MVP is web-only with optional CLI fallback; TUI is postponed.
  - Long-running corpus refresh/distill tasks run via Jobs API and return job_id immediately.
  - Search over existing corpus is synchronous via REST.

- Add **“Risks & Mitigations”** and **“Open Questions”** sections to each module spec (at least 2 bullets each).
- Remove over-engineered MVP features from Phase 1 descriptions—shift them to future phases.
- Align **metrics** across all docs with the MVP set from `aster_requirements.md`. Move long-term targets to “Future Targets” sections.
- Add a **Jobs API** description to planning docs: `POST /jobs/{type}`, `GET /jobs/{id}`, status/progress fields, job events. Make clear that long-running tasks run async and return job_id immediately.
- Add **Data Governance & Academic Integrity** section to master plan: source policies, license storage, retractions, attribution defaults.
- Add **Events & Data Contracts** page with canonical event types and JSON examples (paper.found, paper.indexed, paper.summarized, graph.updated, user.feedback).
- Ensure module “Input Dependencies” and “Output Events” match the project-wide event contracts.

## `aster_bloom.md`
- Add note that Bloom v0 will be **web-only** UI with CLI fallback; TUI planned for future.
- Update implementation phases to start with: basic search results list + paper detail (summary + open questions) consuming Jobs API.
- Clarify that Bloom’s search over existing corpus is synchronous; corpus refresh/distill runs via Jobs API.
- Add risks/open questions: dependency availability, scaling search, feedback adoption.
- Remove mobile app, heavy collab features, and multi-interface parity from MVP phase.

## Deliverable
Update each doc to reflect the MVP-first, modulith + thin REST façade (Jobs API), slim workflow for paper search/distillation, and phased rollout plan. Keep original formatting style and tone.
