# Aster Question Miner Module Specification

## 🎯 Module Overview

**aster_question_miner** is the open question extraction engine that identifies and extracts open research questions, future work items, and limitations from academic content. In the MVP phase, it focuses on mining open questions from abstracts or light PDF sections for the slim workflow.

### Related Specs
Related: jobs_api.md · events_and_data_contracts.md

## 🏗️ Architecture

### Core Responsibilities
- **Open Question Extraction**: Identify and extract open research questions from content
- **Future Work Detection**: Find future work and research directions mentioned in papers
- **Limitation Identification**: Extract study limitations and constraints
- **Evidence Linking**: Link questions to supporting evidence from the content

### MVP Constraints
- **Abstract-First**: Focus on extracting questions from abstracts; light PDF parsing is optional
- **Simple Extraction**: Use heuristics + LLM for question extraction without complex NLP
- **Evidence Quotes**: Return questions with evidence quotes from the source text
- **Event-Driven**: Use event contracts for communication (see `events_and_data_contracts.md`)
- **Jobs API Integration**: Long-running mining tasks use Jobs API
- **(a) abstract‑only path; (b) optional light PDF section (Future Work/Limitations).**

### System Design
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│  Content        │───▶│  Question       │───▶│  Question Store │
│  Input          │    │  Miner Core     │    │                 │
│                 │    │                 │    │ • PostgreSQL   │
│ • Abstracts     │    │ • Extractors    │    │ • Cache         │
│ • Light PDF     │    │ • Classifiers   │    │ • Search Index  │
│ • Metadata      │    │ • Validators    │    │                 │
│                 │    │ • Linkers       │    │                 │
└─────────────────┘    └─────────────────┘    └─────────────────┘
                              │
                              ▼
                       ┌─────────────────┐
                       │  Miner API      │
                       │                 │
                       │ • Questions     │
                       │ • Evidence      │
                       │ • Categories    │
                       │ • Confidence    │
                       └─────────────────┘
```

## 📋 Functional Requirements

### MVP Features

#### 1. Open Question Extraction
- **Question Identification**: Identify open research questions in content
- **Future Work Detection**: Find future work and research directions
- **Limitation Extraction**: Extract study limitations and constraints
- **Question Classification**: Categorize questions by type and domain
- **Confidence Scoring**: Assess confidence in question extraction

#### 2. Evidence Linking
- **Evidence Extraction**: Extract supporting text evidence for questions
- **Quote Generation**: Generate accurate quotes from source content
- **Context Preservation**: Maintain context around extracted evidence
- **Source Attribution**: Link evidence to specific content sections
- **Evidence Validation**: Validate evidence relevance and accuracy

#### 3. Question Processing
- **Question Cleaning**: Clean and normalize extracted questions
- **Duplicate Detection**: Identify and merge duplicate questions
- **Question Ranking**: Rank questions by importance and relevance
- **Category Assignment**: Assign categories to questions
- **Quality Assessment**: Assess overall question quality

#### 4. Basic Analytics
- **Question Statistics**: Track question extraction statistics
- **Category Analysis**: Analyze question categories and trends
- **Quality Metrics**: Monitor extraction quality over time
- **Performance Tracking**: Track processing performance

### Future Phase Features

#### 5. Advanced Extraction (Future Phase)
- **Deep Question Analysis**: More sophisticated question understanding
- **Multi-modal Extraction**: Extract questions from figures and tables
- **Cross-paper Linking**: Link related questions across papers
- **Temporal Analysis**: Track question evolution over time
- **Impact Assessment**: Assess potential impact of questions

#### 6. Advanced Classification (Future Phase)
- **Hierarchical Classification**: Complex question hierarchies
- **Domain-Specific Categories**: Specialized categories for different fields
- **Difficulty Assessment**: Assess question difficulty and complexity
- **Novelty Detection**: Identify novel and groundbreaking questions
- **Collaboration Potential**: Assess collaboration opportunities

#### 7. Advanced Analytics (Future Phase)
- **Trend Analysis**: Analyze question trends over time
- **Gap Analysis**: Identify research gaps from questions
- **Impact Prediction**: Predict potential impact of questions
- **Collaboration Mapping**: Map collaboration opportunities
- **Funding Relevance**: Assess funding relevance of questions

## 🔧 Technical Specifications

### Technology Stack
- **Framework**: FastAPI for REST API, Celery for background processing
- **AI Models**: OpenAI GPT-4, Claude for question extraction
- **NLP Libraries**: spaCy, NLTK for text processing
- **Database**: PostgreSQL for questions, Redis for caching
- **Jobs API**: Integration with Jobs API for long-running tasks

### Prompt Contract
```json
{
  "question": "...",
  "evidence_text": "...",
  "confidence": 0.0
}
```

### Data Models

```python
@dataclass
class OpenQuestion:
    id: str
    content_id: str
    question_text: str
    question_type: str  # 'open_question', 'future_work', 'limitation'
    category: str
    confidence: float
    evidence_quotes: List[str]
    context: str
    extracted_at: datetime
    metadata: Dict[str, Any]

@dataclass
class QuestionEvidence:
    id: str
    question_id: str
    quote_text: str
    source_section: str
    confidence: float
    context_before: str
    context_after: str
    extracted_at: datetime

@dataclass
class QuestionCategory:
    id: str
    name: str
    description: str
    parent_category: Optional[str]
    domain: str
    is_active: bool

@dataclass
class MiningConfig:
    id: str
    name: str
    extraction_rules: Dict[str, Any]
    confidence_thresholds: Dict[str, float]
    question_types: List[str]
    categories: List[str]
    is_active: bool

@dataclass
class MiningJob:
    job_id: str
    content_id: str
    status: str  # 'queued', 'processing', 'completed', 'failed'
    questions_found: int
    processing_time: Optional[float]
    error_message: Optional[str]
    created_at: datetime
    completed_at: Optional[datetime]
```

### API Endpoints

```python
# Question Management
POST /api/v1/questions/extract
GET /api/v1/questions/{content_id}
PUT /api/v1/questions/{question_id}
DELETE /api/v1/questions/{question_id}

# Evidence Management
GET /api/v1/evidence/{question_id}
POST /api/v1/evidence/validate
PUT /api/v1/evidence/{evidence_id}

# Category Management
POST /api/v1/categories
GET /api/v1/categories
PUT /api/v1/categories/{category_id}
DELETE /api/v1/categories/{category_id}

# Jobs API Integration
POST /api/v1/jobs/mine-questions
GET /api/v1/jobs/{job_id}
GET /api/v1/jobs/mining-history

# Batch Operations
POST /api/v1/batch/extract
POST /api/v1/batch/classify
POST /api/v1/batch/validate

# Analytics
GET /api/v1/analytics/statistics
GET /api/v1/analytics/categories
GET /api/v1/analytics/quality
GET /api/v1/analytics/performance

# Search and Discovery
POST /api/v1/search/questions
GET /api/v1/search/suggestions
GET /api/v1/search/related
```

## 🚀 Implementation Phases

### MVP Phase 1: Core Infrastructure (Weeks 1-2)
- Set up FastAPI application with Celery workers
- Implement database models for questions and evidence
- Create basic AI model integration framework
- Set up configuration management system
- Implement Jobs API integration

### MVP Phase 2: Question Extraction (Weeks 3-4)
- Implement basic question extraction using AI models
- Add question classification and categorization
- Create confidence scoring mechanisms
- Build question cleaning and normalization

### MVP Phase 3: Evidence Linking (Weeks 5-6)
- Implement evidence extraction and linking
- Add quote generation and context preservation
- Create evidence validation mechanisms
- Build source attribution system

### MVP Phase 4: Quality and Analytics (Weeks 7-8)
- Implement question quality assessment
- Add duplicate detection and merging
- Create basic analytics and statistics
- Build performance monitoring

### Future Phase 5: Advanced Extraction (Weeks 9-12)
- Implement deep question analysis
- Add multi-modal extraction capabilities
- Create cross-paper linking
- Build temporal analysis features

### Future Phase 6: Advanced Features (Weeks 13-16)
- Implement advanced classification
- Add impact assessment capabilities
- Create collaboration mapping
- Build funding relevance analysis

## 📊 Success Metrics

### MVP Metrics
- **Extraction Speed**: < 30 seconds per abstract
- **Question Quality**: 80%+ relevance score for extracted questions
- **Evidence Accuracy**: 85%+ accuracy in evidence linking
- **Processing Throughput**: 200+ abstracts per hour
- **User Satisfaction**: 7/10+ user rating for question quality

### Future Targets
- **Performance**: < 60 seconds per paper
- **Question Quality**: 90%+ relevance score for extracted questions
- **Evidence Accuracy**: 95%+ accuracy in evidence linking
- **Processing Throughput**: 500+ documents per hour
- **User Satisfaction**: 8/10+ user rating for question quality

## 🔄 Integration Points

### Input Dependencies
- **aster_indexer**: Processed content with embeddings
- **aster_digestor**: Content summaries and tags
- **aster_watcher**: Raw content metadata
- **Configuration Service**: AI model settings and preferences
- **Jobs API**: For long-running mining tasks

### Output Events
```python
# Questions mined (see events_and_data_contracts.md)
QuestionsMinedEvent:
    event: "paper.questions_mined"
    paper_id: str
    questions: List[Dict[str, Any]]
    timestamp: datetime

# Question extracted
QuestionExtractedEvent:
    content_id: str
    question_count: int
    question_types: List[str]
    confidence_avg: float
    processing_time: float

# Evidence linked
EvidenceLinkedEvent:
    question_id: str
    evidence_count: int
    evidence_quality: float
    timestamp: datetime
```

### Downstream Consumers
- **aster_bloom**: Display questions in user interface
- **aster_ranker**: Use questions for relevance scoring
- **aster_pulse**: Send notifications about new questions
- **aster_mapper**: Use questions for knowledge graph construction

## 🛡️ Security & Privacy

### Data Protection
- **Content Encryption**: Encrypt sensitive question data
- **Access Control**: Role-based access to question content
- **Audit Logging**: Track all question extraction operations
- **Data Retention**: Configurable retention for question data

### Privacy Compliance
- **Content Anonymization**: Remove PII from extracted questions
- **User Consent**: Respect user preferences for content processing
- **Data Minimization**: Only extract necessary question data
- **Right to Deletion**: Support question data removal requests

## 🧪 Testing Strategy

### Unit Tests
- Question extraction accuracy validation
- Evidence linking verification
- Classification accuracy testing
- Quality assessment validation

### Integration Tests
- End-to-end question mining workflow
- AI model integration testing
- Database operation validation
- Performance benchmarking

### Quality Assurance
- **Human Evaluation**: Manual review of extracted questions
- **A/B Testing**: Compare different extraction approaches
- **User Feedback**: Collect and incorporate user feedback
- **Continuous Improvement**: Iterative model refinement

## 📚 Documentation Requirements

### API Documentation
- OpenAPI specification with examples
- Question extraction parameters
- Evidence linking configuration
- Classification options

### Operational Documentation
- AI model configuration guide
- Performance tuning recommendations
- Quality monitoring setup
- Backup and recovery procedures

### User Documentation
- Question interpretation guide
- Evidence analysis methods
- Category system usage
- Integration examples and tutorials

## ⚠️ Risks & Mitigations

### Technical Risks
- **AI Model Quality**: Question extraction quality may vary with different content types
  - *Mitigation*: Implement quality validation and fallback mechanisms
- **Processing Bottlenecks**: AI model calls may become rate-limited
  - *Mitigation*: Implement intelligent caching and request queuing
- **Content Complexity**: Complex academic content may be difficult to process
  - *Mitigation*: Use specialized prompts and validation rules
- **Hallucination**: mitigate via evidence requirement & refusal when evidence missing

### Operational Risks
- **API Rate Limits**: External AI APIs may impose strict rate limits
  - *Mitigation*: Implement intelligent caching and request queuing
- **Data Quality**: Poor quality content may lead to poor question extraction
  - *Mitigation*: Implement quality filters and validation rules
- **Question Relevance**: Extracted questions may not be relevant to users
  - *Mitigation*: Implement relevance scoring and user feedback mechanisms

## ❓ Open Questions

### Technical Questions
- What is the optimal prompt structure for question extraction?
- How should we handle technical jargon and domain-specific terminology?
- What is the best approach for validating question quality?
- How should we balance question quantity vs. quality?

### Product Questions
- What types of questions are most valuable for researchers?
- How should we prioritize different question categories?
- What level of question detail is optimal for user engagement?
- How should we handle conflicting or contradictory questions?

### Integration Questions
- How should we coordinate question extraction across multiple content types?
- What is the optimal event schema for question mining?
- How should we handle extraction failures and retries?
- What is the best approach for handling large batches of content?

---

*This specification provides the foundation for building the aster_question_miner module as a separate project. The module will be developed independently and integrated with the broader Aster ecosystem through well-defined APIs and event contracts, starting with an MVP focus on extracting open questions from abstracts.*
