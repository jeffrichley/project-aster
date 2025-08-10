# Aster Indexer Module Specification

## 🎯 Module Overview

**aster_indexer** is the content processing engine that transforms raw academic content into searchable, embeddable representations. In the MVP phase, it focuses on processing abstracts and generating embeddings using PostgreSQL + pgvector for the slim workflow.

### Related Specs
Related: jobs_api.md · events_and_data_contracts.md

## 🏗️ Architecture

### Core Responsibilities
- **Content Processing**: Extract and clean text from academic abstracts
- **Embedding Generation**: Create semantic vector representations using AI models
- **Indexing**: Build searchable indexes using PostgreSQL + pgvector
- **Metadata Enrichment**: Enhance content with basic entity extraction

### MVP Constraints
- **Abstract-Only**: Process abstracts and metadata only; full PDF parsing is optional
- **Simple Embeddings**: Use single embedding model for abstracts
- **PostgreSQL + pgvector**: Use PostgreSQL with pgvector extension for vector storage
- **Event-Driven**: Use event contracts for communication (see `events_and_data_contracts.md`)
- **Jobs API Integration**: Long-running processing tasks use Jobs API
- **Interactive search uses existing corpus (sync); new discovery/refresh runs via **Jobs API** (async).**

### System Design
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│  Event Stream   │───▶│  Indexer Core   │───▶│  Vector Store   │
│                 │    │                 │    │                 │
│ • Paper Events  │    │ • Processors    │    │ • PostgreSQL   │
│ • Abstract Data │    │ • Embedders     │    │ • pgvector      │
│ • Update Events │    │ • Indexers      │    │ • Search API    │
└─────────────────┘    └─────────────────┘    └─────────────────┘
                              │
                              ▼
                       ┌─────────────────┐
                       │  Search API     │
                       │                 │
                       │ • Semantic      │
                       │ • Keyword       │
                       │ • Hybrid        │
                       └─────────────────┘
```

## 📋 Functional Requirements

### MVP Features

#### 1. Content Processing Pipeline
- **Abstract Processing**: Extract and clean text from paper abstracts
- **Metadata Extraction**: Extract authors, dates, citations, and references
- **Content Cleaning**: Remove formatting artifacts and normalize text
- **Language Detection**: Identify and handle multilingual content
- **Basic Validation**: Validate required fields and content quality

#### 2. Embedding Generation
- **Text Embeddings**: Generate semantic vectors for abstracts
- **Single Model**: Use one embedding model for consistency
- **Batch Processing**: Optimize embedding generation for efficiency
- **Incremental Updates**: Efficiently update embeddings for modified content
- **Quality Validation**: Validate embedding quality and dimensions

#### 3. Semantic Indexing
- **Vector Search**: Enable semantic similarity search using pgvector
- **Hybrid Search**: Combine semantic and keyword-based search
- **Basic Filtering**: Support filtering by metadata attributes
- **Real-time Indexing**: Update indexes as new content arrives
- **Performance Optimization**: Maintain search performance with large datasets

#### 4. Basic Content Enrichment
- **Entity Extraction**: Identify people, organizations, and technical terms
- **Topic Assignment**: Basic topic assignment based on keywords
- **Citation Analysis**: Extract basic citation relationships
- **Quality Scoring**: Assess content quality and completeness

### Future Phase Features

#### 5. Advanced Content Processing (Future Phase)
- **PDF Processing**: Extract text, figures, and tables from PDF documents
- **Code Analysis**: Parse and index code repositories and snippets
- **Multi-modal Embeddings**: Handle text, code, and figure combinations
- **Advanced Entity Extraction**: More sophisticated entity recognition

#### 6. Advanced Embedding Models (Future Phase)
- **Multiple Models**: Support different embedding models for different content types
- **Code Embeddings**: Create embeddings for code snippets and repositories
- **Domain-Specific Models**: Use specialized models for different research areas
- **Fine-tuned Models**: Custom models trained on academic content

#### 7. Advanced Indexing (Future Phase)
- **Multiple Index Types**: Support different index types for different use cases
- **Advanced Faceted Search**: Complex filtering and aggregation
- **Real-time Analytics**: Live analytics on search patterns
- **Index Optimization**: Advanced optimization techniques

## 🔧 Technical Specifications

### Technology Stack
- **Framework**: FastAPI for REST API, Celery for background processing
- **Vector Database**: PostgreSQL + pgvector for semantic search
- **Search Engine**: PostgreSQL full-text search for hybrid search
- **AI Models**: OpenAI embeddings, SentenceTransformers
- **Jobs API**: Integration with Jobs API for long-running tasks

### Data Models

```python
@dataclass
class ProcessedContent:
    id: str
    original_id: str
    content_type: str  # 'paper', 'abstract'
    title: str
    abstract: str
    metadata: Dict[str, Any]
    embedding: List[float]  # Single embedding vector
    entities: List[Entity]
    topics: List[str]
    quality_score: float
    processing_timestamp: datetime

@dataclass
class Entity:
    text: str
    entity_type: str  # 'person', 'organization', 'technology'
    confidence: float
    position: Optional[Tuple[int, int]]

@dataclass
class SearchIndex:
    id: str
    name: str
    index_type: str  # 'semantic', 'keyword', 'hybrid'
    vector_dimensions: int
    document_count: int
    last_updated: datetime
    configuration: Dict[str, Any]

@dataclass
class EmbeddingConfig:
    model_name: str
    model_type: str  # 'text'
    dimensions: int
    batch_size: int
    max_length: int
    device: str  # 'cpu', 'gpu'

@dataclass
class ProcessingJob:
    job_id: str
    content_id: str
    status: str  # 'queued', 'processing', 'completed', 'failed'
    processing_time: Optional[float]
    error_message: Optional[str]
    created_at: datetime
    completed_at: Optional[datetime]
```

### API Endpoints

```python
# Content Processing
POST /api/v1/content/process
GET /api/v1/content/{content_id}
PUT /api/v1/content/{content_id}/reprocess
DELETE /api/v1/content/{content_id}

# Embedding Management
POST /api/v1/embeddings/generate
GET /api/v1/embeddings/{content_id}
PUT /api/v1/embeddings/update
DELETE /api/v1/embeddings/{content_id}

# Search Operations
POST /api/v1/search/semantic
POST /api/v1/search/keyword
POST /api/v1/search/hybrid
GET /api/v1/search/suggest

# Index Management
GET /api/v1/indexes
POST /api/v1/indexes
PUT /api/v1/indexes/{index_id}
DELETE /api/v1/indexes/{index_id}

# Jobs API Integration
POST /api/v1/jobs/process-content
GET /api/v1/jobs/{job_id}
GET /api/v1/jobs/processing-history

# Processing Status
GET /api/v1/processing/status
GET /api/v1/processing/queue
POST /api/v1/processing/priority
```

## 🚀 Implementation Phases

### MVP Phase 1: Core Infrastructure (Weeks 1-2)
- Set up FastAPI application with Celery workers
- Implement database models for processed content
- Create basic configuration management
- Set up PostgreSQL + pgvector connections
- Implement Jobs API integration

### MVP Phase 2: Content Processing (Weeks 3-4)
- Implement abstract text extraction pipeline
- Add metadata extraction logic
- Create content cleaning and normalization
- Build basic validation and quality scoring

### MVP Phase 3: Embedding Generation (Weeks 5-6)
- Integrate OpenAI embedding API
- Implement SentenceTransformers for text
- Create batch processing pipeline
- Add embedding quality validation

### MVP Phase 4: Search & Indexing (Weeks 7-8)
- Implement vector search using pgvector
- Add hybrid search with PostgreSQL full-text search
- Create basic faceted search and filtering
- Build real-time indexing system

### Future Phase 5: Advanced Processing (Weeks 9-12)
- Implement PDF text extraction pipeline
- Add code parsing and analysis
- Create advanced entity extraction
- Build multi-modal embedding support

### Future Phase 6: Advanced Features (Weeks 13-16)
- Implement multiple embedding models
- Add advanced indexing techniques
- Create real-time analytics
- Build performance optimization features

## 📊 Success Metrics

### MVP Metrics
- **Processing Speed**: < 15 seconds per abstract
- **Embedding Quality**: 85%+ semantic similarity accuracy
- **Search Latency**: < 200ms for semantic search, < 100ms for keyword search
- **Indexing Throughput**: 500+ abstracts per hour
- **Text Extraction**: 95%+ accuracy for abstract content

### Future Targets
- **Performance**: < 30 seconds per paper, < 5 minutes per repository
- **Embedding Quality**: 90%+ semantic similarity accuracy
- **Search Latency**: < 100ms for semantic search, < 50ms for keyword search
- **Indexing Throughput**: 1000+ documents per hour
- **Entity Recognition**: 85%+ precision and recall

Aligned to master-plan.md → MVP Metrics.

## 🔄 Integration Points

### Input Dependencies
- **aster_watcher**: Raw content from academic sources
- **Configuration Service**: Model settings and API credentials
- **User Preferences**: Content processing priorities and filters
- **Jobs API**: For long-running processing tasks

### Output Events
```python
# Content processed (see events_and_data_contracts.md)
PaperIndexedEvent:
    event: "paper.indexed"
    paper_id: str
    chunks: int
    embedding_model: str
    timestamp: datetime

# Embedding updated
EmbeddingUpdatedEvent:
    content_id: str
    model_name: str
    dimensions: int
    timestamp: datetime

# Search performed
SearchPerformedEvent:
    query: str
    search_type: str
    results_count: int
    response_time: float
```

### Downstream Consumers
- **aster_digestor**: Use processed content for summarization
- **aster_ranker**: Leverage embeddings for relevance scoring
- **aster_mapper**: Build knowledge graphs from entities
- **aster_bloom**: Provide search capabilities to users

## 🛡️ Security & Privacy

### Data Protection
- **Content Encryption**: Encrypt sensitive content at rest
- **Access Control**: Role-based access to processed content
- **Audit Logging**: Track all content processing operations
- **Data Retention**: Configurable retention policies

### Privacy Compliance
- **Content Anonymization**: Remove PII from processed content
- **User Consent**: Respect user preferences for content processing
- **Data Minimization**: Only process necessary content
- **Right to Deletion**: Support content removal requests

## 🧪 Testing Strategy

### Unit Tests
- Content processing pipeline validation
- Embedding generation accuracy testing
- Search functionality verification
- Index management operations

### Integration Tests
- End-to-end content processing workflow
- PostgreSQL + pgvector integration testing
- Search performance benchmarking
- Jobs API integration testing

### Performance Tests
- **Load Testing**: High-volume content processing
- **Stress Testing**: Large document handling
- **Memory Testing**: Embedding generation efficiency
- **Concurrency Testing**: Parallel processing capabilities

## 📚 Documentation Requirements

### API Documentation
- OpenAPI specification with examples
- Search query syntax documentation
- Embedding model configuration guide
- Error handling and troubleshooting

### Operational Documentation
- Deployment and scaling guide
- Performance tuning recommendations
- Monitoring and alerting setup
- Backup and recovery procedures

### User Documentation
- Search interface usage guide
- Content processing configuration
- Embedding model selection guide
- Integration examples and tutorials

## ⚠️ Risks & Mitigations

### Technical Risks
- **Vector Database Performance**: pgvector may not scale to millions of embeddings
  - *Mitigation*: Implement efficient indexing and consider sharding strategies
- **Embedding Model Quality**: Single embedding model may not capture all semantic nuances
  - *Mitigation*: Use high-quality models and implement quality validation
- **Processing Bottlenecks**: Embedding generation may become a bottleneck
  - *Mitigation*: Implement efficient batching and parallel processing

### Operational Risks
- **API Rate Limits**: External embedding APIs may impose rate limits
  - *Mitigation*: Implement intelligent caching and request queuing
- **Data Quality**: Ingested content may be low quality or irrelevant
  - *Mitigation*: Implement quality filters and validation rules
- **Storage Growth**: Vector storage may grow rapidly
  - *Mitigation*: Implement efficient storage strategies and archiving

## ❓ Open Questions

### Technical Questions
- What is the optimal embedding model for academic abstracts?
- How should we handle multilingual content in the MVP phase?
- What is the best approach for incremental embedding updates?
- How should we balance embedding quality vs. processing speed?

### Product Questions
- What level of semantic search accuracy is sufficient for researchers?
- How should we prioritize different types of content for processing?
- What filtering criteria are most important for search results?
- How should we handle content that fails processing?

### Integration Questions
- How should we coordinate processing across multiple content types?
- What is the optimal event schema for content processing?
- How should we handle processing failures and retries?
- What is the best approach for handling large batches of content?

---

*This specification provides the foundation for building the aster_indexer module as a separate project. The module will be developed independently and integrated with the broader Aster ecosystem through well-defined APIs and event contracts, starting with an MVP focus on abstract processing with PostgreSQL + pgvector.*
