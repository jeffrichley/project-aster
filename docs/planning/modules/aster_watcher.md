# Aster Watcher Module Specification

## 🎯 Module Overview

**aster_watcher** is the data ingestion engine that continuously monitors academic sources to feed the Aster research companion with fresh, relevant content. In the MVP phase, it focuses on arXiv and Semantic Scholar (S2) as primary sources.

### Related Specs
Related: jobs_api.md · events_and_data_contracts.md

## 🏗️ Architecture

### Core Responsibilities
- **Source Monitoring**: Real-time tracking of academic publications from arXiv and S2
- **Data Extraction**: Structured extraction of metadata and abstracts
- **Quality Control**: Validation and deduplication of ingested content
- **Event Publishing**: Broadcasting new content to downstream modules via event contracts

### MVP Constraints
- **Limited Sources**: Focus on arXiv and Semantic Scholar (S2) only
- **Abstract-Only**: Extract metadata and abstracts; full PDF parsing is optional
- **Simple Processing**: Basic validation and deduplication without complex filtering
- **Event-Driven**: Use event contracts for communication (see `events_and_data_contracts.md`)
- **Jobs API Integration**: Long-running ingestion tasks use Jobs API
- **Interactive search uses existing corpus (sync); new discovery/refresh runs via **Jobs API** (async).**

### System Design
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Source APIs   │───▶│  Watcher Core   │───▶│  Event System   │
│                 │    │                 │    │                 │
│ • arXiv API     │    │ • Scheduler     │    │ • Event Bus     │
│ • S2 API        │    │ • Extractors    │    │ • Event Store   │
│                 │    │ • Validators    │    │ • Jobs API      │
│                 │    │ • Publishers    │    │                 │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

## 📋 Functional Requirements

### MVP Features

#### 1. Academic Source Monitoring
- **arXiv**: Monitor new submissions in specified categories
- **Semantic Scholar (S2)**: Track publications and citations
- **Basic Filtering**: Filter by research areas and keywords
- **Rate Limiting**: Respect API rate limits and quotas

#### 2. Data Extraction & Processing
- **Metadata Extraction**: Title, authors, abstract, DOI, publication date
- **Abstract Processing**: Extract and clean abstract text
- **Citation Analysis**: Basic citation extraction when available
- **Author Information**: Track author names and affiliations

#### 3. Quality Control
- **Deduplication**: Detect and remove duplicate papers
- **Validation**: Validate required fields (title, abstract, authors)
- **Filtering**: Basic relevance filtering based on keywords
- **Error Handling**: Graceful handling of API failures

### Future Phase Features

#### 4. Extended Source Monitoring (Future Phase)
- **IEEE Xplore**: Track publications in selected journals/conferences
- **ACM Digital Library**: Follow proceedings and journals
- **SpringerLink**: Monitor book chapters and journal articles
- **Google Scholar**: Track citations and related papers
- **Conference Sites**: Monitor ICRA, IROS, RSS, IEEE CASE updates

#### 5. Repository & Code Monitoring (Future Phase)
- **GitHub**: Track repositories by topic, author, or organization
- **GitLab**: Monitor research code repositories
- **arXiv Code**: Extract code links from papers
- **Research Software**: Track software releases and updates

#### 6. Industry & Blog Monitoring (Future Phase)
- **Tech Blogs**: Monitor research-focused blogs and newsletters
- **Company Research**: Track research publications from tech companies
- **Preprint Servers**: Monitor bioRxiv, medRxiv, etc.
- **Research Platforms**: Track Papers With Code

#### 7. Advanced Data Extraction (Future Phase)
- **Full Text Extraction**: Extract full text when available
- **Code Repository Linking**: Link papers to code repositories
- **Advanced Citation Analysis**: Extract and validate references
- **Author Profile Tracking**: Track author profiles and affiliations

## 🔧 Technical Specifications

### Technology Stack
- **Framework**: FastAPI for REST API, Celery for background tasks
- **Database**: PostgreSQL for metadata, Redis for caching
- **Event System**: Event contracts for module communication
- **API Clients**: Async HTTP clients for external APIs
- **Jobs API**: Integration with Jobs API for long-running tasks

### Data Models

```python
@dataclass
class AcademicPaper:
    id: str
    title: str
    authors: List[str]
    abstract: str
    doi: Optional[str]
    publication_date: datetime
    source: str  # 'arxiv', 's2'
    categories: List[str]
    keywords: List[str]
    pdf_url: Optional[str]
    citations: List[str]
    references: List[str]
    metadata: Dict[str, Any]

@dataclass
class SourceConfig:
    source_type: str  # 'arxiv', 's2'
    enabled: bool
    update_frequency: timedelta
    filters: Dict[str, Any]
    credentials: Optional[Dict[str, str]]

@dataclass
class IngestionJob:
    job_id: str
    source_type: str
    status: str  # 'queued', 'running', 'completed', 'failed'
    papers_found: int
    papers_processed: int
    errors: List[str]
    started_at: datetime
    completed_at: Optional[datetime]
```

### API Endpoints

```python
# Source Management
POST /api/v1/sources
GET /api/v1/sources
PUT /api/v1/sources/{source_id}
DELETE /api/v1/sources/{source_id}

# Content Queries
GET /api/v1/papers
GET /api/v1/papers/{paper_id}
GET /api/v1/papers/search

# Monitoring Control
POST /api/v1/monitoring/start
POST /api/v1/monitoring/stop
GET /api/v1/monitoring/status

# Jobs API Integration
POST /api/v1/jobs/ingest-corpus
GET /api/v1/jobs/{job_id}
GET /api/v1/jobs/ingestion-history

Accepts **Idempotency-Key** to dedupe.
```

## 🚀 Implementation Phases

### MVP Phase 1: Core Infrastructure (Weeks 1-2)
- Set up FastAPI application structure
- Implement database models and migrations
- Create basic configuration management
- Set up logging and monitoring
- Implement Jobs API integration

### MVP Phase 2: arXiv Integration (Weeks 3-4)
- Implement arXiv API integration
- Create metadata extraction pipeline
- Build basic validation and deduplication
- Implement event publishing

### MVP Phase 3: S2 Integration (Weeks 5-6)
- Implement Semantic Scholar API integration
- Add citation extraction
- Create unified paper model
- Build source coordination

### MVP Phase 4: Event System (Weeks 7-8)
- Implement event contract publishing
- Create event subscription system
- Add real-time notifications
- Build monitoring dashboard

### Future Phase 5: Extended Sources (Weeks 9-12)
- Implement IEEE Xplore monitoring
- Add ACM Digital Library connector
- Create repository monitoring
- Build advanced filtering

### Future Phase 6: Advanced Features (Weeks 13-16)
- Implement full text extraction
- Add code repository linking
- Create advanced citation analysis
- Build author profile tracking

## 📊 Success Metrics

### MVP Metrics
- **Latency**: < 5 minutes from source update to event publication
- **Throughput**: Process 100+ papers per hour
- **Reliability**: 99% uptime with automatic retry logic
- **Accuracy**: 90%+ successful content extraction
- **Deduplication**: 95%+ duplicate detection rate

### Future Targets
- **Performance**: Process 1000+ papers per hour
- **Reliability**: 99.9% uptime with graceful degradation
- **Completeness**: 95%+ metadata extraction success
- **Relevance**: 85%+ content passes initial relevance filters
- **Freshness**: < 30 minutes delay for high-priority sources

## 🔄 Integration Points

### Input Dependencies
- **Configuration Service**: Source settings and credentials
- **User Preferences**: Research interests and topic filters
- **Authentication Service**: API credentials and rate limits
- **Jobs API**: For long-running ingestion tasks

### Output Events
```python
# New paper discovered (see events_and_data_contracts.md)
PaperFoundEvent:
    event: "paper.found"
    paper_id: str
    title: str
    source: str
    url: str
    doi: Optional[str]
    authors: List[str]
    published_date: datetime
    license: str
    timestamp: datetime

# Ingestion job status
IngestionJobEvent:
    job_id: str
    source_type: str
    status: str
    papers_found: int
    papers_processed: int
    errors: List[str]
    timestamp: datetime

# Source status change
SourceStatusEvent:
    source_id: str
    status: str  # 'online', 'offline', 'error'
    message: str
    timestamp: datetime
```

### Downstream Consumers
- **aster_indexer**: Process new content for embedding
- **aster_digestor**: Generate summaries and tags
- **aster_ranker**: Score relevance to user interests
- **aster_pulse**: Send notifications for important updates

## 🛡️ Security & Compliance

### Data Protection
- **Encryption**: Encrypt sensitive API credentials
- **Access Control**: Role-based access to source configurations
- **Audit Logging**: Track all data access and modifications
- **Rate Limiting**: Respect API rate limits and quotas

### Compliance
- **Terms of Service**: Respect source website terms
- **Rate Limiting**: Implement polite crawling practices
- **Data Retention**: Configurable retention policies
- **Privacy**: Anonymize user data in logs

## 🧪 Testing Strategy

### Unit Tests
- API endpoint testing with pytest
- Data extraction logic validation
- Event publishing verification
- Configuration management testing

### Integration Tests
- External API integration testing
- Database operation validation
- Event system end-to-end testing
- Performance benchmarking

### Monitoring & Alerting
- **Health Checks**: API endpoint availability
- **Performance Metrics**: Response times and throughput
- **Error Tracking**: Failed extractions and API errors
- **Resource Usage**: Memory, CPU, and network utilization

## 📚 Documentation Requirements

### API Documentation
- OpenAPI/Swagger specification
- Example requests and responses
- Error code documentation
- Rate limiting information

### Operational Documentation
- Deployment guide
- Configuration reference
- Troubleshooting guide
- Performance tuning guide

### User Documentation
- Source configuration guide
- Filter and query examples
- Event subscription setup
- Integration examples

## ⚠️ Risks & Mitigations

### Technical Risks
- **API Rate Limits**: External APIs may impose strict rate limits
  - *Mitigation*: Implement intelligent caching, request queuing, and exponential backoff
- **Rate limits**: backoff + API quotas.
- **Source ToS**: respect robots.txt / official APIs.
- **Data Quality**: Ingested content may be low quality or irrelevant
  - *Mitigation*: Implement quality filters and validation rules
- **Event System Failures**: Event publishing may fail
  - *Mitigation*: Implement retry logic and event persistence

### Operational Risks
- **Source Availability**: External APIs may become unavailable
  - *Mitigation*: Implement multiple fallback sources and graceful degradation
- **Data Volume**: Large volumes of papers may overwhelm the system
  - *Mitigation*: Implement efficient processing and storage strategies
- **API Changes**: External APIs may change their interfaces
  - *Mitigation*: Implement version detection and adapter patterns

## ❓ Open Questions

### Technical Questions
- How should we handle API rate limits across multiple sources?
- What is the optimal strategy for deduplication across sources?
- How should we prioritize papers from different sources?
- What is the best approach for handling API failures and retries?

### Product Questions
- Which sources provide the highest quality content for different research areas?
- How should we balance coverage vs. quality in source selection?
- What filtering criteria are most important for researchers?
- How should we handle papers in different languages?

### Integration Questions
- How should we coordinate ingestion across multiple sources?
- What is the optimal event schema for paper discovery?
- How should we handle conflicts between different source data?
- What is the best approach for incremental updates vs. full refreshes?

---

*This specification provides the foundation for building the aster_watcher module as a separate project. The module will be developed independently and integrated with the broader Aster ecosystem through well-defined APIs and event contracts, starting with an MVP focus on arXiv and S2 sources.*
