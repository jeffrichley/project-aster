# Aster Mapper Module Specification

## 🎯 Module Overview

**aster_mapper** is the knowledge graph construction and visualization engine that builds comprehensive maps of research relationships, citations, and conceptual connections. It provides researchers with visual insights into the research landscape and helps identify connections between different areas of study.

### Related Specs
Related: jobs_api.md · events_and_data_contracts.md

## 🏗️ Architecture

### Core Responsibilities
- **Knowledge Graph Construction**: Build comprehensive research knowledge graphs
- **Relationship Mapping**: Identify and map relationships between research entities
- **Visualization Engine**: Create interactive visual representations of knowledge graphs
- **Graph Analytics**: Provide analytical insights from graph structures

### System Design

#### Original ASCII Block Diagram
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│  Content &      │───▶│  Mapper Core    │───▶│  Graph Store    │
│  Entity Data    │    │                 │    │                 │
│                 │    │ • Builders      │    │ • Neo4j         │
│ • Papers        │    │ • Analyzers     │    │ • ArangoDB      │
│ • Authors       │    │ • Visualizers   │    │ • GraphQL       │
│ • Institutions  │    │ • Optimizers    │    │ • Cache         │
└─────────────────┘    └─────────────────┘    └─────────────────┘
                              │
                              ▼
                       ┌─────────────────┐
                       │  Mapper API     │
                       │                 │
                       │ • Graph         │
                       │ • Visualization │
                       │ • Analytics     │
                       │ • Navigation    │
                       └─────────────────┘
```

#### Detailed Internal Architecture
```mermaid
graph TB
    subgraph "aster_mapper Internal Components"
        api[API Layer<br/>FastAPI REST API]
        builders[Graph Builders<br/>Entity/Relationship Construction]
        analyzers[Graph Analyzers<br/>Centrality/Community Detection]
        visualizers[Graph Visualizers<br/>D3.js/Cytoscape.js]
        optimizers[Graph Optimizers<br/>Performance/Indexing]
        storage[Graph Storage<br/>Neo4j/ArangoDB]
        cache[Cache Layer<br/>Redis]
    end

    subgraph "Graph Processing"
        networkx[NetworkX<br/>Graph Algorithms]
        igraph[igraph<br/>Graph Analysis]
        sklearn[scikit-learn<br/>Community Detection]
    end

    subgraph "Visualization Libraries"
        d3[D3.js<br/>Interactive Graphs]
        cytoscape[Cytoscape.js<br/>Graph Visualization]
    end

    subgraph "Graph Databases"
        neo4j[Neo4j<br/>Primary Graph DB]
        arango[ArangoDB<br/>Alternative Graph DB]
        graphql[GraphQL<br/>Query Interface]
    end

    subgraph "Event Stream"
        kafka[Apache Kafka<br/>Event Stream]
    end

    %% Internal flow
    api --> builders
    api --> analyzers
    api --> visualizers
    api --> optimizers

    builders --> storage
    analyzers --> storage
    visualizers --> storage
    optimizers --> cache

    %% Graph processing integration
    networkx --> analyzers
    igraph --> analyzers
    sklearn --> analyzers

    %% Visualization integration
    d3 --> visualizers
    cytoscape --> visualizers

    %% Database connections
    storage --> neo4j
    storage --> arango
    storage --> graphql

    %% Event consumption
    kafka --> builders
    kafka --> analyzers

    %% Styling
    classDef internal fill:#e8f5e8,stroke:#2e7d32,stroke-width:2px
    classDef graph fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px
    classDef viz fill:#e1f5fe,stroke:#01579b,stroke-width:2px
    classDef db fill:#fff3e0,stroke:#f57c00,stroke-width:2px
    classDef events fill:#fce4ec,stroke:#c2185b,stroke-width:2px

    class api,builders,analyzers,visualizers,optimizers,storage,cache internal
    class networkx,igraph,sklearn graph
    class d3,cytoscape viz
    class neo4j,arango,graphql db
    class kafka events
```

#### External Dependencies
- **Input Data**: Content entities from aster_indexer, insights from aster_digestor
- **Graph Databases**: Neo4j, ArangoDB for graph storage
- **Graph Processing**: NetworkX, igraph, scikit-learn for graph algorithms
- **Visualization**: D3.js, Cytoscape.js for interactive graph visualization

#### Integration Points
- **Event Consumers**: aster_visualizer, aster_bloom, aster_pulse
- **Events Published**:
  - `GraphUpdatedEvent`: Knowledge graph updates and changes
  - `AnalyticsCompletedEvent`: Graph analytics results
  - `VisualizationCreatedEvent`: Graph visualizations generated

## 📋 Functional Requirements

### 1. Knowledge Graph Construction
- **Entity Extraction**: Extract papers, authors, institutions, and concepts
- **Relationship Identification**: Identify citations, collaborations, and conceptual links
- **Graph Building**: Construct comprehensive knowledge graphs
- **Graph Maintenance**: Keep graphs updated with new content
- **Graph Optimization**: Optimize graph structure for performance

### 2. Relationship Mapping
- **Citation Networks**: Map citation relationships between papers
- **Author Networks**: Map collaboration and co-author relationships
- **Concept Networks**: Map conceptual relationships and topic clusters
- **Institutional Networks**: Map institutional collaborations
- **Temporal Networks**: Map relationships over time

### 3. Visualization Engine
- **Interactive Graphs**: Create interactive, zoomable graph visualizations
- **Multiple Layouts**: Support different graph layout algorithms
- **Filtering & Search**: Allow filtering and searching within graphs
- **Custom Views**: Support custom graph views and perspectives
- **Export Options**: Export graphs in various formats

### 4. Graph Analytics
- **Centrality Analysis**: Identify central nodes and influential entities
- **Community Detection**: Identify research communities and clusters
- **Path Analysis**: Find paths between different entities
- **Impact Analysis**: Analyze impact and influence patterns
- **Trend Analysis**: Identify trends and patterns in the graph

### 5. Navigation & Discovery
- **Graph Navigation**: Navigate through the knowledge graph
- **Entity Exploration**: Explore entities and their relationships
- **Path Finding**: Find paths between different research areas
- **Similarity Search**: Find similar entities and relationships
- **Recommendation Engine**: Recommend related entities and paths

## 🔧 Technical Specifications

### Technology Stack
- **Graph Database**: Neo4j or ArangoDB for graph storage
- **Graph Processing**: NetworkX, igraph for graph algorithms
- **Visualization**: D3.js, Cytoscape.js for interactive visualizations
- **Framework**: FastAPI for REST API, GraphQL for graph queries
- **ML Libraries**: scikit-learn for community detection and clustering
- **Analytics**: Pandas, NumPy for graph analytics
- **Caching**: Redis for graph query caching

### Data Models

```python
@dataclass
class GraphEntity:
    id: str
    entity_type: str  # 'paper', 'author', 'institution', 'concept'
    name: str
    properties: Dict[str, Any]
    metadata: Dict[str, Any]
    created_at: datetime
    updated_at: datetime

@dataclass
class GraphRelationship:
    id: str
    source_id: str
    target_id: str
    relationship_type: str  # 'cites', 'collaborates', 'belongs_to', 'similar_to'
    properties: Dict[str, Any]
    weight: float  # relationship strength
    created_at: datetime

@dataclass
class GraphView:
    id: str
    user_id: str
    name: str
    view_type: str  # 'citation', 'collaboration', 'concept', 'temporal'
    filters: Dict[str, Any]
    layout_config: Dict[str, Any]
    entities: List[str]
    relationships: List[str]
    created_at: datetime

@dataclass
class GraphAnalytics:
    id: str
    graph_id: str
    analysis_type: str  # 'centrality', 'community', 'path', 'impact'
    parameters: Dict[str, Any]
    results: Dict[str, Any]
    computed_at: datetime
    performance_metrics: Dict[str, float]
```

### API Endpoints

```python
# Graph Management
POST /api/v1/graphs
GET /api/v1/graphs
GET /api/v1/graphs/{graph_id}
PUT /api/v1/graphs/{graph_id}
DELETE /api/v1/graphs/{graph_id}

# Entity Management
POST /api/v1/entities
GET /api/v1/entities
GET /api/v1/entities/{entity_id}
PUT /api/v1/entities/{entity_id}
DELETE /api/v1/entities/{entity_id}

# Relationship Management
POST /api/v1/relationships
GET /api/v1/relationships
GET /api/v1/relationships/{relationship_id}
PUT /api/v1/relationships/{relationship_id}
DELETE /api/v1/relationships/{relationship_id}

# Graph Queries
POST /api/v1/graphs/{graph_id}/query
GET /api/v1/graphs/{graph_id}/entities
GET /api/v1/graphs/{graph_id}/relationships
POST /api/v1/graphs/{graph_id}/paths
GET /api/v1/graphs/{graph_id}/similar

# Visualization
POST /api/v1/visualizations/create
GET /api/v1/visualizations/{viz_id}
PUT /api/v1/visualizations/{viz_id}
DELETE /api/v1/visualizations/{viz_id}
POST /api/v1/visualizations/{viz_id}/export

# Analytics
POST /api/v1/analytics/centrality
POST /api/v1/analytics/community
POST /api/v1/analytics/path
POST /api/v1/analytics/impact
GET /api/v1/analytics/{analysis_id}/results

# Graph Views
POST /api/v1/views
GET /api/v1/views/{user_id}
PUT /api/v1/views/{view_id}
DELETE /api/v1/views/{view_id}
POST /api/v1/views/{view_id}/share

# Navigation & Discovery
POST /api/v1/navigation/explore
GET /api/v1/navigation/paths
POST /api/v1/navigation/similar
GET /api/v1/navigation/recommendations
```

## 🚀 Implementation Phases

### Phase 1: Core Infrastructure (Weeks 1-2)
- Set up FastAPI application with GraphQL support
- Implement graph database integration
- Create basic entity and relationship models
- Set up graph processing pipeline

### Phase 2: Graph Construction (Weeks 3-4)
- Implement entity extraction from content
- Add relationship identification algorithms
- Create graph building and maintenance system
- Build graph optimization features

### Phase 3: Visualization Engine (Weeks 5-6)
- Implement interactive graph visualization
- Add multiple layout algorithms
- Create filtering and search capabilities
- Build custom view system

### Phase 4: Analytics & Navigation (Weeks 7-8)
- Implement graph analytics algorithms
- Add navigation and discovery features
- Create recommendation engine
- Build export and integration capabilities

## 📊 Success Metrics

### Performance Metrics
- **Graph Construction**: < 5 minutes for 1000+ entity graphs
- **Query Performance**: < 100ms for complex graph queries
- **Visualization Rendering**: < 2 seconds for large graph visualizations
- **Analytics Computation**: < 30 seconds for complex analytics

### Quality Metrics
- **Entity Accuracy**: 95%+ accuracy in entity extraction
- **Relationship Quality**: 90%+ accuracy in relationship identification
- **Visualization Clarity**: 85%+ user satisfaction with visualizations
- **Analytics Insight**: 80%+ usefulness of generated insights

### User Experience Metrics
- **Navigation Efficiency**: 50%+ improvement in research discovery
- **Insight Discovery**: 60%+ new insights discovered through graphs
- **User Engagement**: 70%+ engagement with graph visualizations
- **Collaboration Discovery**: 40%+ new collaboration opportunities identified

## 🔄 Integration Points

### Input Dependencies
- **aster_indexer**: Content entities and relationships
- **aster_digestor**: Content insights and tags
- **aster_ranker**: Relevance and ranking data
- **aster_verifier**: Validated references and citations
- **aster_bloom**: User interaction and exploration data

### Output Events
```python
# Graph updated
GraphUpdatedEvent:
    graph_id: str
    entities_added: int
    relationships_added: int
    update_type: str  # 'incremental', 'full_rebuild'
    timestamp: datetime

# Analytics completed
AnalyticsCompletedEvent:
    analysis_id: str
    analysis_type: str
    results_summary: Dict[str, Any]
    performance_metrics: Dict[str, float]
    timestamp: datetime

# Visualization created
VisualizationCreatedEvent:
    viz_id: str
    graph_id: str
    view_type: str
    entities_count: int
    relationships_count: int
    timestamp: datetime
```

### Downstream Consumers
- **aster_bloom**: Display graph visualizations and insights
- **aster_ranker**: Use graph relationships for better ranking
- **aster_pulse**: Send insights and discovery notifications
- **aster_gardener**: Learn from graph exploration patterns

## 🛡️ Security & Privacy

### Data Protection
- **Graph Data Encryption**: Encrypt sensitive graph data
- **Access Control**: Secure access to graph data and analytics
- **Query Security**: Prevent malicious graph queries
- **Audit Logging**: Track all graph operations and queries

### Privacy Compliance
- **Entity Anonymization**: Support anonymous entity representation
- **User Consent**: Clear consent for graph data usage
- **Data Minimization**: Only include necessary graph data
- **Right to Deletion**: Support complete graph data removal

## 🧪 Testing Strategy

### Unit Tests
- Entity extraction accuracy validation
- Relationship identification testing
- Graph algorithm verification
- Visualization rendering testing

### Integration Tests
- End-to-end graph construction workflow
- Graph database integration testing
- Visualization system validation
- Analytics computation testing

### Performance Tests
- **Load Testing**: Large graph construction and querying
- **Visualization Performance**: Large graph rendering testing
- **Analytics Performance**: Complex analytics computation
- **Scalability Testing**: System performance under load

## 📚 Documentation Requirements

### API Documentation
- OpenAPI specification with examples
- GraphQL schema documentation
- Graph query language guide
- Analytics API documentation

### User Documentation
- Graph visualization usage guide
- Navigation and discovery tutorial
- Analytics interpretation guide
- Custom view creation guide

### Developer Documentation
- Graph database setup guide
- Graph algorithm implementation
- Visualization framework guide
- Performance optimization guide

---

*This specification provides the foundation for building the aster_mapper module as a separate project. The module will be developed independently and integrated with the broader Aster ecosystem through well-defined APIs and event contracts.*
