# Events & Data Contracts

## Overview
Defines the canonical events exchanged between Aster modules and their JSON schemas. All modules should publish and consume events using these contracts to ensure interoperability.

---

## Core Event Types

### Paper Found
```json
{
  "event": "paper.found",
  "paper_id": "arxiv:2401.12345",
  "title": "Multi-Agent Connectivity in Dynamic Environments",
  "source": "arxiv",
  "url": "https://arxiv.org/abs/2401.12345",
  "doi": null,
  "authors": ["Alice Smith", "Bob Jones"],
  "published_date": "2024-01-15",
  "license": "arxiv-oa",
  "timestamp": "2025-08-09T14:00:00Z"
}
```

### Paper Indexed
```json
{
  "event": "paper.indexed",
  "paper_id": "arxiv:2401.12345",
  "chunks": 8,
  "embedding_model": "text-embedding-3-large",
  "timestamp": "2025-08-09T14:05:00Z"
}
```

### Paper Summarized
```json
{
  "event": "paper.summarized",
  "paper_id": "arxiv:2401.12345",
  "summary": {
    "tl_dr": "A method for maintaining connectivity in multi-agent systems in open water.",
    "bullets": [
      "Introduces dynamic positioning algorithm",
      "Uses free-space path loss model"
    ]
  },
  "timestamp": "2025-08-09T14:06:00Z"
}
```

### Open Question Mined
```json
{
  "event": "paper.questions_mined",
  "paper_id": "arxiv:2401.12345",
  "questions": [
    {
      "text": "How can the algorithm be adapted to high-interference environments?",
      "evidence_text": "Future work should examine the effect of interference.",
      "confidence": 0.9
    }
  ],
  "timestamp": "2025-08-09T14:07:00Z"
}
```

### Graph Updated
```json
{
  "event": "graph.updated",
  "changes": [
    {"type": "add_node", "node_id": "topic:connectivity"},
    {"type": "add_edge", "from": "paper:arxiv:2401.12345", "to": "topic:connectivity"}
  ],
  "timestamp": "2025-08-09T14:08:00Z"
}
```

### User Feedback
```json
{
  "event": "user.feedback",
  "user_id": "user-123",
  "paper_id": "arxiv:2401.12345",
  "label": "useful",
  "note": "Relevant to my open water test scenario",
  "timestamp": "2025-08-09T14:10:00Z"
}
```

---

## Implementation Notes
- Events are JSON-serializable, UTF-8 encoded.
- All events **must** include a `timestamp` in ISO 8601 format.
- Event names are lowercase with dot separators: `{entity}.{action}`.
- Consumers should ignore unknown fields for forward compatibility.
