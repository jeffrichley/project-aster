# Aster Digestor Module Specification

## 🎯 Module Overview

**aster_digestor** is the content summarization and tagging engine that transforms processed academic content into human-readable summaries, extracts key insights, and applies intelligent tagging for easy discovery and organization. In the MVP phase, it focuses on generating structured summaries from abstracts for the slim workflow.

### Related Specs
Related: jobs_api.md · events_and_data_contracts.md

## 🏗️ Architecture

### Core Responsibilities
- **Content Summarization**: Generate concise, accurate summaries of academic papers from abstracts
- **Intelligent Tagging**: Apply relevant tags and categories to content
- **Insight Extraction**: Identify key findings, methodologies, and contributions
- **Content Structuring**: Organize content into digestible formats for researchers

### MVP Constraints
- **Abstract-Only**: Generate summaries from abstracts only; full PDF processing is optional
- **Structured Summaries**: Focus on structured summary formats (TL;DR, bullets, key points)
- **Simple Tagging**: Basic topic and methodology tagging without complex hierarchies
- **Event-Driven**: Use event contracts for communication (see `events_and_data_contracts.md`)
- **Jobs API Integration**: Long-running summarization tasks use Jobs API
- **Abstracts-first; light PDF sections optional (Future Work/Limitations).**

### System Design
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│  Processed      │───▶│  Digestor Core  │───▶│  Summary Store  │
│  Content        │    │                 │    │                 │
│                 │    │ • Summarizers   │    │ • PostgreSQL   │
│ • Abstracts     │    │ • Taggers       │    │ • Cache         │
│ • Metadata      │    │ • Extractors    │    │ • Search Index  │
│                 │    │ • Structurers   │    │                 │
└─────────────────┘    └─────────────────┘    └─────────────────┘
                              │
                              ▼
                       ┌─────────────────┐
                       │  Digest API     │
                       │                 │
                       │ • Summary       │
                       │ • Tags          │
                       │ • Insights      │
                       │ • Structure     │
                       └─────────────────┘
```

## 📋 Functional Requirements

### MVP Features

#### 1. Content Summarization
- **TL;DR Generation**: Create concise one-sentence summaries
- **Key Points Extraction**: Identify main contributions and findings
- **Methodology Summary**: Summarize research methods and approaches
- **Results Highlighting**: Extract key results and implications
- **Structured Format**: Generate summaries in consistent, structured formats

#### 2. Intelligent Tagging
- **Topic Classification**: Assign research topics and categories
- **Methodology Tags**: Tag papers by research methodology
- **Technology Tags**: Identify technologies, frameworks, and tools
- **Application Tags**: Categorize by application domain
- **Basic Confidence Scoring**: Assess tag confidence levels

#### 3. Insight Extraction
- **Key Findings**: Extract main research findings and conclusions
- **Gap Identification**: Identify research gaps and future work
- **Contribution Analysis**: Highlight novel contributions
- **Limitation Recognition**: Identify study limitations and constraints
- **Basic Implication Analysis**: Extract practical implications

#### 4. Content Structuring
- **Section Organization**: Structure content into logical sections
- **Basic Tagging**: Create simple tag relationships
- **Cross-referencing**: Link related content and concepts
- **Quality Assessment**: Basic quality scoring for summaries

### Future Phase Features

#### 5. Advanced Summarization (Future Phase)
- **Multi-level Summaries**: Generate different length summaries for different use cases
- **Full Text Processing**: Process full PDF content for comprehensive summaries
- **Multi-modal Summaries**: Handle text, figures, and tables
- **Domain-Specific Summaries**: Specialized summaries for different research areas

#### 6. Advanced Tagging (Future Phase)
- **Hierarchical Tagging**: Create complex tag hierarchies and relationships
- **Novelty Scoring**: Assess and tag content novelty and impact
- **Advanced Classification**: More sophisticated topic and methodology classification
- **Dynamic Tagging**: Adaptive tagging based on user feedback

#### 7. Advanced Insight Extraction (Future Phase)
- **Deep Implication Analysis**: Extract complex theoretical and practical implications
- **Timeline Mapping**: Organize content by temporal relationships
- **Impact Assessment**: Structure content by impact and significance
- **Advanced Cross-referencing**: Complex linking of related concepts

## 🔧 Technical Specifications

### Technology Stack
- **Framework**: FastAPI for REST API, Celery for background processing
- **AI Models**: OpenAI GPT-4, Claude for summarization
- **NLP Libraries**: spaCy, NLTK for text processing
- **Database**: PostgreSQL for summaries, Redis for caching
- **Jobs API**: Integration with Jobs API for long-running tasks

### Data Models

```python
@dataclass
class ContentSummary:
    id: str
    content_id: str
    summary_type: str  # 'tl_dr', 'key_points', 'methodology', 'results'
    content: str
    length: int
    confidence_score: float
    generated_at: datetime
    model_used: str
    metadata: Dict[str, Any]

@dataclass
class ContentTag:
    id: str
    content_id: str
    tag_name: str
    tag_type: str  # 'topic', 'methodology', 'technology', 'application'
    confidence: float
    source: str  # 'ai', 'user', 'rule_based'

@dataclass
class ContentInsight:
    id: str
    content_id: str
    insight_type: str  # 'finding', 'gap', 'contribution', 'limitation'
    text: str
    confidence: float
    supporting_evidence: List[str]
    implications: List[str]
    extracted_at: datetime

@dataclass
class DigestConfig:
    summary_lengths: Dict[str, int]  # 'short', 'medium', 'long'
    tag_categories: List[str]
    insight_types: List[str]
    model_preferences: Dict[str, str]
    quality_thresholds: Dict[str, float]

@dataclass
class SummarizationJob:
    job_id: str
    content_id: str
    status: str  # 'queued', 'processing', 'completed', 'failed'
    summary_types: List[str]
    processing_time: Optional[float]
    error_message: Optional[str]
    created_at: datetime
    completed_at: Optional[datetime]
```

### API Endpoints

```python
# Summary Management
POST /api/v1/summaries/generate
GET /api/v1/summaries/{content_id}
PUT /api/v1/summaries/{summary_id}
DELETE /api/v1/summaries/{summary_id}

# Tagging Operations
POST /api/v1/tags/apply
GET /api/v1/tags/{content_id}
PUT /api/v1/tags/{tag_id}
DELETE /api/v1/tags/{tag_id}

# Insight Extraction
POST /api/v1/insights/extract
GET /api/v1/insights/{content_id}
PUT /api/v1/insights/{insight_id}
DELETE /api/v1/insights/{insight_id}

# Content Structuring
POST /api/v1/structure/organize
GET /api/v1/structure/{content_id}
PUT /api/v1/structure/{structure_id}

# Jobs API Integration
POST /api/v1/jobs/summarize-content
GET /api/v1/jobs/{job_id}
GET /api/v1/jobs/summarization-history

# Batch Operations
POST /api/v1/batch/summarize
POST /api/v1/batch/tag
POST /api/v1/batch/extract

# Quality Control
GET /api/v1/quality/score/{content_id}
POST /api/v1/quality/review
GET /api/v1/quality/feedback
```

## 🚀 Implementation Phases

### MVP Phase 1: Core Infrastructure (Weeks 1-2)
- Set up FastAPI application with Celery workers
- Implement database models for summaries and tags
- Create basic AI model integration framework
- Set up configuration management system
- Implement Jobs API integration

### MVP Phase 2: Summarization Engine (Weeks 3-4)
- Implement OpenAI GPT-4 integration for summarization
- Add structured summary formats (TL;DR, key points)
- Create summary quality assessment
- Build summary caching and optimization

### MVP Phase 3: Tagging System (Weeks 5-6)
- Implement intelligent tagging with AI models
- Add basic tag organization
- Create tag confidence scoring
- Build tag recommendation system

### MVP Phase 4: Insight Extraction (Weeks 7-8)
- Implement insight extraction algorithms
- Add basic cross-referencing capabilities
- Create insight quality validation
- Build insight aggregation and analysis

### Future Phase 5: Advanced Features (Weeks 9-12)
- Implement multi-level summarization
- Add hierarchical tag organization
- Create advanced insight extraction
- Build full text processing capabilities

### Future Phase 6: Advanced Integration (Weeks 13-16)
- Implement multi-modal summarization
- Add domain-specific processing
- Create advanced cross-referencing
- Build dynamic tagging systems

## 📊 Success Metrics

### MVP Metrics
- **Summarization Speed**: < 15 seconds per abstract
- **Tagging Accuracy**: 85%+ precision and recall for topic classification
- **Insight Quality**: 80%+ relevance score for extracted insights
- **Processing Throughput**: 300+ abstracts per hour
- **Summary Readability**: 8/10+ readability score

### Future Targets
- **Performance**: < 30 seconds per paper, < 2 minutes per repository
- **Tagging Accuracy**: 90%+ precision and recall for topic classification
- **Insight Quality**: 85%+ relevance score for extracted insights
- **Processing Throughput**: 500+ documents per hour
- **User Satisfaction**: 8/10+ user rating for digest quality

Aligned to master-plan.md → MVP Metrics.

## 🔄 Integration Points

### Input Dependencies
- **aster_indexer**: Processed content with embeddings
- **aster_watcher**: Raw content metadata
- **Configuration Service**: AI model settings and preferences
- **User Preferences**: Summary style and tag preferences
- **Jobs API**: For long-running summarization tasks

### Output Events
```python
# Summary generated (see events_and_data_contracts.md)
PaperSummarizedEvent:
    event: "paper.summarized"
    paper_id: str
    summary: Dict[str, Any]  # TL;DR, bullets, etc.
    timestamp: datetime

# Tags applied
TagsAppliedEvent:
    content_id: str
    tags_count: int
    tag_types: List[str]
    confidence_avg: float

# Insights extracted
InsightsExtractedEvent:
    content_id: str
    insights_count: int
    insight_types: List[str]
    quality_score: float
```

### Downstream Consumers
- **aster_ranker**: Use summaries and tags for relevance scoring
- **aster_bloom**: Display summaries and tags in user interface
- **aster_pulse**: Send digest notifications to users
- **aster_mapper**: Use insights for knowledge graph construction

## 🛡️ Security & Privacy

### Data Protection
- **Content Encryption**: Encrypt sensitive summaries and insights
- **Access Control**: Role-based access to digest content
- **Audit Logging**: Track all digest operations and modifications
- **Data Retention**: Configurable retention for digest data

### Privacy Compliance
- **Content Anonymization**: Remove PII from summaries
- **User Consent**: Respect user preferences for content processing
- **Data Minimization**: Only extract necessary insights
- **Right to Deletion**: Support digest content removal

## 🧪 Testing Strategy

### Unit Tests
- Summarization quality validation
- Tagging accuracy testing
- Insight extraction verification
- Content structuring validation

### Integration Tests
- End-to-end digest generation workflow
- AI model integration testing
- Database operation validation
- Performance benchmarking

### Quality Assurance
- **Human Evaluation**: Manual review of summaries and tags
- **A/B Testing**: Compare different summarization approaches
- **User Feedback**: Collect and incorporate user feedback
- **Continuous Improvement**: Iterative model refinement

## 📚 Documentation Requirements

### API Documentation
- OpenAPI specification with examples
- Summary generation parameters
- Tagging configuration guide
- Insight extraction options

### Operational Documentation
- AI model configuration guide
- Performance tuning recommendations
- Quality monitoring setup
- Backup and recovery procedures

### User Documentation
- Summary interpretation guide
- Tag system usage
- Insight analysis methods
- Integration examples and tutorials

## ⚠️ Risks & Mitigations

### Technical Risks
- **AI Model Quality**: Summarization quality may vary with different content types
  - *Mitigation*: Implement quality validation and fallback mechanisms
- **Processing Bottlenecks**: AI model calls may become rate-limited
  - *Mitigation*: Implement intelligent caching and request queuing
- **Content Complexity**: Complex academic content may be difficult to summarize accurately
  - *Mitigation*: Use specialized prompts and validation rules

### Operational Risks
- **API Rate Limits**: External AI APIs may impose strict rate limits
  - *Mitigation*: Implement intelligent caching and request queuing
- **Data Quality**: Poor quality abstracts may lead to poor summaries
  - *Mitigation*: Implement quality filters and validation rules
- **Model Bias**: AI models may introduce bias in summaries and tags
  - *Mitigation*: Implement bias detection and mitigation strategies

## ❓ Open Questions

### Technical Questions
- What is the optimal prompt structure for academic summarization?
- How should we handle technical jargon and domain-specific terminology?
- What is the best approach for validating summary quality?
- How should we balance summary length vs. information density?

### Product Questions
- What level of summarization detail is most useful for researchers?
- How should we prioritize different types of insights for extraction?
- What tagging granularity is optimal for content discovery?
- How should we handle conflicting or contradictory information?

### Integration Questions
- How should we coordinate summarization across multiple content types?
- What is the optimal event schema for summary generation?
- How should we handle summarization failures and retries?
- What is the best approach for handling large batches of content?

---

*This specification provides the foundation for building the aster_digestor module as a separate project. The module will be developed independently and integrated with the broader Aster ecosystem through well-defined APIs and event contracts, starting with an MVP focus on structured summaries from abstracts.*
