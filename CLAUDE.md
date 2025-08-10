# Aster: Autonomous Research Companion

> An autonomous, modular AI research companion that spans the entire PhD lifecycle—finding, indexing, summarizing, mapping, verifying, and organizing academic knowledge while providing collaborative tools, personalized insights, and intelligent research workflow automation.

## Agent OS Documentation

### Product Context
- **Mission & Vision:** @.agent-os/product/mission.md
- **Technical Architecture:** @.agent-os/product/tech-stack.md
- **Development Roadmap:** @.agent-os/product/roadmap.md
- **Decision History:** @.agent-os/product/decisions.md

### Development Standards
- **Code Style:** @~/.agent-os/standards/code-style.md
- **Best Practices:** @~/.agent-os/standards/best-practices.md

### Project Management
- **Active Specs:** @.agent-os/specs/
- **Spec Planning:** Use `@~/.agent-os/instructions/create-spec.md`
- **Tasks Execution:** Use `@~/.agent-os/instructions/execute-tasks.md`

## Workflow Instructions

When asked to work on this codebase:

1. **First**, check @.agent-os/product/roadmap.md for current priorities
2. **Then**, follow the appropriate instruction file:
   - For new features: @~/.agent-os/instructions/create-spec.md
   - For tasks execution: @~/.agent-os/instructions/execute-tasks.md
3. **Always**, adhere to the standards in the files listed above

## Important Notes

- Product-specific files in `.agent-os/product/` override any global standards
- User's specific instructions override (or amend) instructions found in `.agent-os/specs/...`
- Always adhere to established patterns, code style, and best practices documented above.

## Project Aster Overview

An autonomous, modular AI research companion that spans the entire PhD lifecycle—finding, indexing, summarizing, mapping, verifying, and organizing academic knowledge while providing collaborative tools, personalized insights, and intelligent research workflow automation.

### Key Features
- **Modern Python Tooling**: Built with uv, nox, and just for optimal development experience
- **Quality Assurance**: Comprehensive testing, linting, and type checking
- **Documentation**: Automated documentation generation with Sphinx
- **CI/CD Ready**: Pre-configured GitHub Actions workflows
- **Type Safety**: Full type hints and mypy integration
- **Extensible Architecture**: Modular design with plugin support

### Technology Stack
- **Package Manager**: uv for fast dependency management
- **Testing**: pytest with comprehensive coverage reporting
- **Linting**: ruff for fast Python linting
- **Formatting**: black for consistent code formatting
- **Type Checking**: mypy for static type analysis
- **Documentation**: Sphinx with modern theme
- **CI/CD**: GitHub Actions with quality gates

## MVP Architecture

The MVP follows a **slim workflow** with 6 core modules:

```
aster_watcher (arXiv/S2) →
aster_indexer (abstracts only, pgvector) →
aster_digestor (structured summary from abstract) →
aster_question_miner (abstract or light PDF section) →
aster_ranker (simple recency + similarity) →
aster_bloom (web UI search & detail)
```

### MVP Constraints
- **Abstract-only processing** - No heavy PDF parsing
- **PostgreSQL + pgvector** - No Kafka/Neo4j/Elasticsearch in v0
- **Web-only UI** - No mobile app, TUI postponed
- **Simple ranking** - Recency + similarity, minimal personalization
- **Jobs API** - Async tasks for long-running operations
- **Synchronous search** - Search over existing corpus via REST

### Development Phases
1. **MVP Foundation** (Weeks 1-8): Slim workflow implementation
2. **Intelligence & Learning** (Weeks 9-16): User learning and advanced features
3. **User Experience & Collaboration** (Weeks 17-24): Collaboration and writing tools
4. **Specialized Research Tools** (Weeks 25-32): Experiment tracking and visualization
5. **Platform & Enterprise** (Weeks 33-40): Multi-tenant and enterprise features

## Current Status

**Phase**: Planning
**Focus**: MVP Foundation - Data Ingestion & Processing
**Timeline**: 8 weeks for MVP completion
**Priority**: Establish slim workflow with core data processing capabilities

## Success Metrics

### MVP Metrics
- **Paper ingestion latency**: < 5 min from arXiv posting to visible dashboard
- **Embedding + summary time**: < 15 sec/paper
- **Relevance ranking F1-score**: > 0.8 compared to manual tagging
- **User satisfaction (self-reported)**: 9/10+ in productivity & motivation

### Future Targets
- **Performance**: < 5s response time for dashboard queries
- **Reliability**: 99.9% uptime with graceful degradation
- **Scalability**: Support 1000+ concurrent users
- **Data Quality**: 95%+ accuracy in paper classification
- **User Experience**: 80%+ daily active usage, 70%+ feature adoption
