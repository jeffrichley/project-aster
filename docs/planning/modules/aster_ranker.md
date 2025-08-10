# Aster Ranker Module Specification

## 🎯 Module Overview

**aster_ranker** is the relevance scoring and content ranking engine that evaluates academic content against user research interests, preferences, and current work context to provide personalized content recommendations and priority ordering. In the MVP phase, it focuses on simple recency + similarity ranking for the slim workflow.

### Related Specs
Related: jobs_api.md · events_and_data_contracts.md

## 🏗️ Architecture

### Core Responsibilities
- **Relevance Scoring**: Calculate content relevance to user research interests
- **Personalized Ranking**: Order content based on user preferences and behavior
- **Simple Ranking**: Use recency and similarity for content prioritization
- **Context-Aware Recommendations**: Consider current research context

### MVP Constraints
- **Simple Ranking**: Focus on recency + similarity ranking without complex algorithms
- **Basic Personalization**: Simple user preference matching without advanced learning
- **Event-Driven**: Use event contracts for communication (see `events_and_data_contracts.md`)
- **Jobs API Integration**: Long-running ranking tasks use Jobs API
- **Minimal Dependencies**: Avoid complex ML models in MVP phase
- **Ranking = recency prior + cosine similarity to query/user topic; personalization minimal in MVP.**

### System Design
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│  Content &      │───▶│  Ranker Core    │───▶│  Ranked         │
│  User Data      │    │                 │    │  Content Store  │
│                 │    │ • Scorers       │    │                 │
│ • Papers        │    │ • Rankers       │    │ • PostgreSQL   │
│ • Abstracts     │    │ • Filters       │    │ • Cache         │
│ • User Profile  │    │ • Recommenders  │    │ • Search Index  │
│ • Preferences   │    │                 │    │                 │
└─────────────────┘    └─────────────────┘    └─────────────────┘
                              │
                              ▼
                       ┌─────────────────┐
                       │  Ranking API    │
                       │                 │
                       │ • Relevance     │
                       │ • Recommendations│
                       │ • Priority      │
                       │ • Simple        │
                       └─────────────────┘
```

## 📋 Functional Requirements

### MVP Features

#### 1. Relevance Scoring
- **Keyword Matching**: Score content based on keyword overlap with user interests
- **Semantic Similarity**: Use embeddings to calculate semantic relevance
- **Topic Alignment**: Assess alignment with user's research topics
- **Basic Methodology Relevance**: Score based on research methodology preferences
- **Simple Citation Relevance**: Consider basic citation relationships

#### 2. Simple Ranking
- **Recency Scoring**: Prioritize recent papers and content
- **Similarity Scoring**: Rank based on similarity to user interests
- **Combined Ranking**: Simple weighted combination of recency and similarity
- **Basic Filtering**: Filter content based on user preferences
- **Quality Thresholds**: Basic quality filtering

#### 3. Basic Personalization
- **User Interest Matching**: Match content to user research interests
- **Simple Preference Learning**: Basic learning from user interactions
- **Research Topic Alignment**: Align content with user's research topics
- **Basic Timeline Awareness**: Consider research deadlines

#### 4. Simple Recommendations
- **High-Priority Content**: Identify high-priority content for users
- **Similar Content**: Recommend content similar to user interests
- **Recent Content**: Highlight recent relevant content
- **Basic Reasoning**: Simple explanations for recommendations
- **"Why recommended" signals list (score components) for Bloom integration**

### Future Phase Features

#### 5. Advanced Relevance Scoring (Future Phase)
- **Advanced Keyword Matching**: More sophisticated keyword analysis
- **Deep Semantic Similarity**: Advanced semantic understanding
- **Complex Topic Alignment**: Multi-dimensional topic analysis
- **Advanced Methodology Relevance**: Sophisticated methodology scoring
- **Citation Network Analysis**: Complex citation relationship analysis

#### 6. Advanced Personalization (Future Phase)
- **User Behavior Learning**: Advanced learning from user interactions
- **Research Phase Adaptation**: Sophisticated research phase awareness
- **Collaboration Context**: Team and advisor preference integration
- **Advanced Timeline Awareness**: Complex deadline and milestone awareness
- **Quality Preferences**: Sophisticated quality and source preferences

#### 7. Novelty Detection (Future Phase)
- **Citation Velocity**: Track how quickly papers are being cited
- **Methodology Innovation**: Identify novel research approaches
- **Cross-Domain Impact**: Detect papers with broader impact
- **Author Reputation**: Consider author track record and influence
- **Venue Prestige**: Factor in publication venue reputation

#### 8. Advanced Context-Aware Recommendations (Future Phase)
- **Current Research Context**: Consider ongoing research projects
- **Gap Analysis**: Identify content that fills research gaps
- **Competitive Intelligence**: Track competing research
- **Collaboration Opportunities**: Identify potential collaborators
- **Funding Relevance**: Consider funding agency priorities

## 🔧 Technical Specifications

### Technology Stack
- **Framework**: FastAPI for REST API, Celery for background processing
- **Vector Operations**: NumPy, SciPy for similarity calculations
- **Database**: PostgreSQL for rankings, Redis for caching
- **Jobs API**: Integration with Jobs API for long-running tasks

### Data Models

```python
@dataclass
class RelevanceScore:
    id: str
    content_id: str
    user_id: str
    overall_score: float
    recency_score: float
    similarity_score: float
    topic_score: float
    methodology_score: float
    calculated_at: datetime
    confidence: float

@dataclass
class UserProfile:
    id: str
    user_id: str
    research_interests: List[str]
    methodology_preferences: List[str]
    quality_preferences: Dict[str, float]
    current_projects: List[str]
    last_updated: datetime

@dataclass
class RankingConfig:
    id: str
    name: str
    scoring_weights: Dict[str, float]  # recency, similarity, topic, methodology
    recency_decay_rate: float
    similarity_threshold: float
    update_frequency: timedelta
    is_active: bool

@dataclass
class Recommendation:
    id: str
    user_id: str
    content_id: str
    recommendation_type: str  # 'high_priority', 'similar', 'recent'
    confidence: float
    reasoning: str
    suggested_action: str
    generated_at: datetime

@dataclass
class RankingJob:
    job_id: str
    user_id: str
    status: str  # 'queued', 'processing', 'completed', 'failed'
    content_count: int
    processing_time: Optional[float]
    error_message: Optional[str]
    created_at: datetime
    completed_at: Optional[datetime]
```

### API Endpoints

```python
# Ranking Operations
POST /api/v1/ranking/score
GET /api/v1/ranking/{content_id}
PUT /api/v1/ranking/{score_id}
DELETE /api/v1/ranking/{score_id}

# User Profile Management
POST /api/v1/profiles
GET /api/v1/profiles/{user_id}
PUT /api/v1/profiles/{user_id}
DELETE /api/v1/profiles/{user_id}

# Recommendations
POST /api/v1/recommendations/generate
GET /api/v1/recommendations/{user_id}
PUT /api/v1/recommendations/{rec_id}
DELETE /api/v1/recommendations/{rec_id}

# Jobs API Integration
POST /api/v1/jobs/rank-content
GET /api/v1/jobs/{job_id}
GET /api/v1/jobs/ranking-history

# Batch Operations
POST /api/v1/batch/score
POST /api/v1/batch/recommend
POST /api/v1/batch/update_profiles

# Simple Analysis
POST /api/v1/analysis/similarity
GET /api/v1/analysis/trending
GET /api/v1/analysis/user-patterns
```

## 🚀 Implementation Phases

### MVP Phase 1: Core Infrastructure (Weeks 1-2)
- Set up FastAPI application with Celery workers
- Implement database models for rankings and user profiles
- Create basic scoring framework
- Set up configuration management system
- Implement Jobs API integration

### MVP Phase 2: Simple Scoring Engine (Weeks 3-4)
- Implement recency-based scoring
- Add similarity scoring using embeddings
- Create basic topic alignment algorithms
- Build simple methodology relevance scoring

### MVP Phase 3: Basic Personalization (Weeks 5-6)
- Implement user interest matching
- Add simple preference learning
- Create basic research topic alignment
- Build simple timeline awareness

### MVP Phase 4: Simple Recommendations (Weeks 7-8)
- Implement basic recommendation generation
- Add simple reasoning for recommendations
- Create recommendation quality validation
- Build basic recommendation system

### Future Phase 5: Advanced Scoring (Weeks 9-12)
- Implement advanced keyword matching
- Add deep semantic similarity
- Create complex topic alignment
- Build advanced methodology scoring

### Future Phase 6: Advanced Features (Weeks 13-16)
- Implement novelty detection algorithms
- Add advanced personalization
- Create context-aware recommendations
- Build sophisticated recommendation reasoning

## 📊 Success Metrics

### MVP Metrics
- **Scoring Speed**: < 2 seconds per content item
- **Recommendation Quality**: 75%+ user satisfaction with recommendations
- **Personalization**: 60%+ improvement over non-personalized ranking
- **Relevance Accuracy**: 80%+ precision and recall for relevant content
- **User Engagement**: 50%+ click-through rate on recommendations

### Future Targets
- **Performance**: < 5 seconds per content item
- **Recommendation Quality**: 85%+ user satisfaction with recommendations
- **Novelty Detection**: 90%+ accuracy in identifying novel content
- **Personalization**: 80%+ improvement over non-personalized ranking
- **User Engagement**: 70%+ click-through rate on recommendations

Aligned to master-plan.md → MVP Metrics.

## 🔄 Integration Points

### Input Dependencies
- **aster_indexer**: Content embeddings and metadata
- **aster_digestor**: Content summaries and tags
- **aster_gardener**: User preference learning data
- **User Interface**: User interactions and feedback
- **Jobs API**: For long-running ranking tasks

### Output Events
```python
# Content scored
ContentScoredEvent:
    content_id: str
    user_id: str
    overall_score: float
    component_scores: Dict[str, float]
    ranking_position: int

# Recommendation generated
RecommendationGeneratedEvent:
    user_id: str
    content_id: str
    recommendation_type: str
    confidence: float
    reasoning: str

# Ranking job completed
RankingJobCompletedEvent:
    job_id: str
    user_id: str
    content_count: int
    processing_time: float
    timestamp: datetime
```

### Downstream Consumers
- **aster_bloom**: Display ranked content in user interface
- **aster_pulse**: Send priority notifications based on rankings
- **aster_gardener**: Use ranking feedback for preference learning
- **aster_mapper**: Use rankings for knowledge graph prioritization

## 🛡️ Security & Privacy

### Data Protection
- **User Data Encryption**: Encrypt user profiles and preferences
- **Access Control**: Role-based access to ranking data
- **Audit Logging**: Track all ranking operations and decisions
- **Data Retention**: Configurable retention for ranking data

### Privacy Compliance
- **User Consent**: Respect user preferences for personalization
- **Data Minimization**: Only collect necessary user data
- **Anonymization**: Support anonymous ranking modes
- **Right to Deletion**: Support user data removal requests

## 🧪 Testing Strategy

### Unit Tests
- Scoring algorithm validation
- Ranking consistency testing
- Similarity calculation accuracy
- Personalization effectiveness

### Integration Tests
- End-to-end ranking workflow
- User profile integration testing
- Recommendation quality validation
- Performance benchmarking

### A/B Testing
- **Algorithm Comparison**: Test different ranking approaches
- **Weight Optimization**: Optimize scoring weights
- **Feature Testing**: Test new ranking features
- **User Experience**: Measure user satisfaction improvements

## 📚 Documentation Requirements

### API Documentation
- OpenAPI specification with examples
- Scoring algorithm documentation
- Recommendation generation guide
- Ranking configuration guide

### Operational Documentation
- Ranking model configuration guide
- Performance tuning recommendations
- User profile management guide
- Monitoring and alerting setup

### User Documentation
- Ranking system explanation
- Recommendation interpretation guide
- Personalization configuration
- Feedback and improvement process

## ⚠️ Risks & Mitigations

### Technical Risks
- **Scoring Performance**: Simple scoring may not capture complex relevance patterns
  - *Mitigation*: Start with proven algorithms and iterate based on user feedback
- **Embedding Quality**: Poor embeddings may lead to poor similarity scores
  - *Mitigation*: Use high-quality embedding models and validate similarity results
- **User Profile Accuracy**: Inaccurate user profiles may lead to poor recommendations
  - *Mitigation*: Implement profile validation and user feedback mechanisms

### Operational Risks
- **Cold Start Problem**: New users may receive poor recommendations initially
  - *Mitigation*: Implement fallback to popular content and quick profile building
- **Bias in Recommendations**: Simple algorithms may introduce bias
  - *Mitigation*: Implement diversity measures and bias detection
- **Scalability Issues**: Ranking may become slow with large content volumes
  - *Mitigation*: Implement efficient indexing and caching strategies

## ❓ Open Questions

### Technical Questions
- What is the optimal weight for recency vs. similarity in the MVP?
- How should we handle users with very specific or very broad interests?
- What is the best approach for handling new content types?
- How should we balance personalization vs. content diversity?

### Product Questions
- What level of personalization is most valuable for researchers?
- How should we handle conflicting user preferences?
- What recommendation frequency is optimal for user engagement?
- How should we measure recommendation success beyond click-through rates?

### Integration Questions
- How should we coordinate ranking across multiple content types?
- What is the optimal event schema for ranking updates?
- How should we handle ranking failures and retries?
- What is the best approach for handling real-time ranking updates?

---

*This specification provides the foundation for building the aster_ranker module as a separate project. The module will be developed independently and integrated with the broader Aster ecosystem through well-defined APIs and event contracts, starting with an MVP focus on simple recency + similarity ranking.*
