## 🩇 Project Aster: Autonomous Research Companion for the PhD Journey

**Tagline**: *Grow your knowledge. One star at a time.*

---

### 📌 Purpose

Project Aster is a next-generation AI platform designed to support and partially automate the full PhD lifecycle — from literature discovery and research planning to writing, experiment tracking, and emotional support. It transforms the PhD process from a solitary, manually intensive endeavor into a structured, assisted, and adaptive workflow, giving researchers more time and mental space for creativity and critical thinking.

---

### 🧠 Core Goals

| Phase                    | Support Capabilities                                                                |
| ------------------------ | ----------------------------------------------------------------------------------- |
| **Literature Discovery** | Continuously watch and extract from scholarly sources, GitHub, and industry updates |
| **Paper Summarization**  | Distill, tag, and map papers to research goals and gaps                             |
| **Research Planning**    | Suggest methods, structure research questions, track milestones                     |
| **Experiment Aid**       | Help design experiments, track data, generate code, debug simulations               |
| **Writing & LaTeX**      | Draft, revise, and compile LaTeX documents locally or via Overleaf                  |
| **Project Tracking**     | Maintain logs, bibliographies, and to-do lists                                      |
| **Motivational Support** | Provide emotional check-ins, encouragement, and reflective insights                 |
| **Collaboration**        | Share dashboards, summaries, and writing with collaborators or advisors             |
| **Citation Analysis**    | Show supportive/contrasting/neutral sentiment of citing papers                      |
| **Knowledge Mapping**    | Display research clusters and gaps in graph visualizations                          |
| **Systematic Review**    | Support notebook-based workflows for semi-automated lit review pipelines            |
| **Reference Auditing**   | Check references for retraction, reliability, or outdated citations                 |
| **Alerts & Monitoring**  | Notify users of new literature, updates from followed authors, or topic spikes      |
| **Study Mode**           | Generate flashcards, quizzes, and learning aids from papers                         |
| **Multilingual Support** | Translate and summarize non-English papers; multilingual input/output               |
| **Visual Analytics**     | Create comparison plots and paper-based dashboards for metrics and trends           |
| **Claim Consensus**      | Summarize consensus across literature clusters for specific claims                  |

---

### 🧹 System Modules

| Module             | Description                                                                                          |
| ------------------ | ---------------------------------------------------------------------------------------------------- |
| `aster_watcher`    | Scrapes and subscribes to academic feeds, GitHub, arXiv, Google Scholar                              |
| `aster_indexer`    | Converts new data into embeddings, clusters them topically                                           |
| `aster_digestor`   | Generates human-readable summaries, tags, and visual mappings                                        |
| `aster_ranker`     | Scores relevance to your dissertation via keyword, novelty, and citation velocity                    |
| `aster_bloom`      | Frontend dashboard to browse content, flag priority items, and connect notes to outline sections     |
| `aster_pulse`      | Sends proactive updates and motivational check-ins via preferred channels                            |
| `aster_scribe`     | Research writer + editor that can generate academic prose in LaTeX or Markdown                       |
| `aster_alchemist`  | Manages experimental tracking, results logging, and LaTeX chart/table generation                     |
| `aster_gardener`   | Learns from your usage patterns, tailoring future suggestions and auto-prioritizing research threads |
| `aster_mapper`     | Builds force-directed citation and concept maps from embedded paper relationships                    |
| `aster_verifier`   | Audits references for retraction status, integrity, and outdated citations                           |
| `aster_tutor`      | Generates flashcards and quizzes from paper sets for self-study or teaching                          |
| `aster_visualizer` | Creates data visualizations (e.g. sample size vs effect size) from extracted research metrics        |

---

### 🚀 Creative Persona: Aster

* **Personality**: Thoughtful, precise, empathetic — your AI research co-pilot.
* **Abilities**:

  * Reads and summarizes literature overnight
  * Suggests missing sections, better phrasing, or supporting citations while writing
  * Monitors project progress and emotional load, offering timely encouragement
  * Chats with you like a labmate — available across desktop, mobile, and voice
* **Taglines**:

  * *“Let’s draft that introduction today — I’ve preloaded your most relevant references.”*
  * *“Hey, take a break. You’ve already read 8 papers today — and flagged 3 top picks.”*

---

### 🗝 User Journey Highlights

**Morning:**

* Wake up to a briefing: “Top 3 new papers found overnight. One may support Chapter 3.”
* Check your dashboard — a new method tagged “hybrid control + sim2real” is highlighted.

**Afternoon:**

* Ask Aster: “Generate a proposal draft for a DARPA submission based on X.”
* Start writing — she auto-cites the recent literature and formats it in Overleaf.

**Evening:**

* Review experimental results; Aster suggests increasing resolution based on trend detection.
* Aster reminds: “You’ve been working for 3 hours straight. Want me to queue up a recap video for tomorrow’s talk?”

---

### 📊 Success Metrics (MVP Phase)

| Metric                            | Goal                                            |
| --------------------------------- | ----------------------------------------------- |
| Paper ingestion latency           | < 5 min from arXiv posting to visible dashboard |
| Embedding + summary time          | < 15 sec/paper                                  |
| Relevance ranking F1-score        | > 0.8 compared to manual tagging                |
| Writing assistant usage           | Used in 70% of thesis sections within 3 months  |
| User satisfaction (self-reported) | 9/10+ in productivity & motivation              |

---

### 🪼 Roadmap Epics (Agile Format)

**EPIC 1: Ingest & Index**

* ☑ Set up feeds + arXiv API
* ☐ Create metadata store (papers, authors, DOIs, tags)
* ☐ Embed & cluster engine
* ☐ Tag relevance engine

**EPIC 2: Dashboard & Summaries**

* ☐ Paper viewer with TL;DR, tags, citation graphs
* ☐ Highlight + note interface
* ☐ Topic filtering + paper ↔ outline linkage

**EPIC 3: Writing & Compilation**

* ☐ Aster assistant for section writing
* ☐ Overleaf + LaTeX compiler integration
* ☐ Reference auto-insertion + citation graph
* ☐ Reference auditing and retraction checker

**EPIC 4: Experiment Tracking & Research Planning**

* ☐ Research question generator
* ☐ Code assistant for data & experiment automation
* ☐ Logging & charting for results (WandB or MLFlow)
* ☐ Visual analytics from structured data

**EPIC 5: Motivation, Wellness, and Scheduling**

* ☐ Encouragement engine (sentiment-driven or scheduled)
* ☐ Calendar integration + deadline tracking
* ☐ Reflective journaling prompts
* ☐ Study mode: flashcards and quizzes

**EPIC 6: Exploration & Monitoring**

* ☐ Visual knowledge graph navigator
* ☐ Citation sentiment analysis module
* ☐ Alerts and updates from tracked authors/topics
* ☐ Claim consensus analyzer
* ☐ Multilingual paper support and summarization

---

### 📦 Deliverables

* ☑ Project README + Branding assets (`aster/`)
* ☐ MCP-compliant modular architecture (LangGraph + Petal tools)
* ☐ Local + cloud-ready config stack (Hydra + OmegaConf)
* ☐ Test suite (unit + behavioral)
* ☐ Demo video: *"A Day with Aster"*
* ☐ Public landing page (aster.watch)

---

### 🧠 Strategic Vision

> *“Aster is not just a tool — she’s a mentor, a writer, a reader, and a friend. She’s how future PhDs stay sane.”*
