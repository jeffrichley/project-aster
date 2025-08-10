# Jobs API Specification

## Overview
The Jobs API provides a consistent interface for triggering long-running tasks asynchronously and retrieving their status, progress, and results.

---

## Job Lifecycle
- **queued** → **running** → **succeeded** OR **failed** OR **canceled**
- Progress updates in percentage (0–100) and optional phase descriptions.
- Partial results may be available before completion.

---

## Endpoints

### Create Job
**POST** `/jobs/{job_type}`

**Path Parameters:**
- `job_type`: Type of job to create (e.g., `search-distill`, `ingest-corpus`)

**Body Example:**
```json
{
  "topic": "multi-agent connectivity",
  "since": "2024-01-01",
  "max_papers": 50
}
```

**Response (202 Accepted):**
```json
{
  "job_id": "c8e5a39c-9b56-44f1-8b37-2396f1f6a6d2"
}
```

---

### Get Job Status
**GET** `/jobs/{job_id}`

**Response Example:**
```json
{
  "job_id": "c8e5a39c-9b56-44f1-8b37-2396f1f6a6d2",
  "status": "running",
  "progress": 42,
  "phase": "summarizing",
  "found": 23,
  "summarized": 9,
  "open_questions": 17,
  "result_ref": "run_2025_08_10_001"
}
```

---

### Cancel Job
**DELETE** `/jobs/{job_id}`

**Response Example:**
```json
{
  "job_id": "c8e5a39c-9b56-44f1-8b37-2396f1f6a6d2",
  "status": "canceled"
}
```

---

## Job Events (Optional Real-time Updates)

**WebSocket or SSE**: `/jobs/{job_id}/stream`

Example event:
```json
{
  "phase": "summarizing",
  "count_done": 7,
  "count_total": 23,
  "message": "Summarizing papers"
}
```

---

## Example Job Types
- `search-distill`: Search for papers, ingest metadata, summarize, mine open questions.
- `ingest-corpus`: Ingest a specified corpus or source list.
- `summarize-papers`: Summarize a list of already indexed papers.
- `mine-questions`: Run the open question miner on existing content.

---

## Notes
- Use **idempotency keys** to prevent duplicate jobs when re-running the same request.
- Store job parameters and results for reproducibility and debugging.
