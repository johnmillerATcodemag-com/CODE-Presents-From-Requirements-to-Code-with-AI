# Create Zeus Academia Project Instructions

## Prompt

Create a project.instructions.md file for the Zeus Academia system that provides comprehensive project setup, development guidelines, and contribution standards for developers working on this academic management platform.

## Context

The Zeus Academia system is a comprehensive academic management platform for universities and higher education institutions. The system manages faculty lifecycle, academic ranks, tenure status, degrees, and various administrative workflows.

**System Overview:**

- **Domain**: Academic personnel management and career progression
- **Scope**: Faculty lifecycle, rank management, course assignments, administrative workflows
- **Architecture**: Enterprise-scale system with ORM-based data model
- **Integration**: HR, payroll, registration, and directory systems
- **Compliance**: Academic standards, legal requirements, accreditation criteria

**Current Project Structure:**

```
├── .github/
│   ├── instructions/           # Development guidelines and standards
│   ├── requirements/           # Domain model and workflow documentation
│   │   ├── orm/               # Entity definitions and relationships
│   │   └── workflows.md       # Business process documentation
│   ├── prompts/               # AI assistant prompt definitions
│   └── chat-modes/            # Specialized AI personas
├── requirements/
│   ├── orm/                   # Objects, roles, and facts
│   └── use-cases/             # Individual workflow use cases
├── slides/                    # Presentation materials
└── README.md                  # Project overview
```

**Established Standards:**

- Use case creation templates (`.github/instructions/create-use-case.instructions.md`)
- Prompt engineering standards (`.github/instructions/prompt-standards.instructions.md`)
- Domain model definitions in ORM format
- Workflow documentation with detailed business rules

## Requirements

The project.instructions.md file should provide:

### Development Environment Setup

- **Prerequisites**: Required tools, frameworks, and development environment
- **Installation**: Step-by-step setup instructions for local development
- **Configuration**: Environment variables, database connections, external integrations
- **Verification**: Commands to validate successful setup
- **Troubleshooting**: Common setup issues and solutions

### Project Standards & Guidelines

- **Code Standards**: Coding conventions, naming patterns, and style guides
- **Architecture Principles**: Layered architecture, dependency injection, separation of concerns
- **Database Guidelines**: ORM usage, migration strategies, data access patterns
- **API Design**: RESTful conventions, versioning, authentication, error handling
- **Testing Standards**: Unit testing, integration testing, test data management
- **Documentation**: Code comments, API documentation, architectural decision records

### Development Workflow

- **Git Workflow**: Branching strategy, commit conventions, pull request process
- **Feature Development**: From requirement analysis to deployment
- **Code Review**: Review checklist, approval process, quality gates
- **Continuous Integration**: Build pipeline, automated testing, deployment process
- **Release Management**: Versioning strategy, release notes, deployment procedures

### Domain-Specific Guidelines

- **Academic Domain Knowledge**: Understanding of higher education processes and terminology
- **Business Rule Implementation**: Faculty rank progression, tenure processes, compliance requirements
- **Data Privacy**: FERPA compliance, academic record protection, access control
- **Integration Patterns**: University system integrations, data synchronization, API contracts
- **Workflow Implementation**: Academic process automation, approval workflows, notification systems

### Quality Assurance

- **Testing Strategy**: Academic scenario testing, edge case validation, compliance verification
- **Performance Standards**: Response time requirements, scalability benchmarks, load testing
- **Security Requirements**: Authentication, authorization, data encryption, audit logging
- **Accessibility**: WCAG compliance, multi-language support, assistive technology compatibility
- **Monitoring**: Application performance monitoring, error tracking, usage analytics

## Output Location

Create the instructions file at: `.github/instructions/project.instructions.md`

## Additional Considerations

**Academic Context Integration:**

- **Institutional Variability**: Guidelines for adapting to different university structures and policies
- **Regulatory Compliance**: Handling of accreditation requirements, legal mandates, union agreements
- **Academic Calendar Integration**: Semester systems, academic year cycles, registration periods
- **Stakeholder Management**: Faculty, administrators, students, compliance officers, IT staff
- **Change Management**: Academic culture sensitivity, training requirements, adoption strategies

**Technical Architecture Considerations:**

- **Scalability**: Multi-institutional deployment, high availability requirements
- **Integration Architecture**: Enterprise service bus patterns, API gateway, microservices considerations
- **Data Management**: Academic data lifecycle, retention policies, archival strategies
- **Security Architecture**: Role-based access control, data classification, incident response
- **Deployment Strategy**: Cloud-native patterns, containerization, infrastructure as code

**Development Team Structure:**

- **Role Definitions**: Product owner, architects, developers, testers, DevOps engineers
- **Skill Requirements**: Academic domain knowledge, technical expertise, compliance awareness
- **Onboarding Process**: Domain education, technical training, mentorship programs
- **Communication Standards**: Stakeholder updates, technical documentation, knowledge sharing
- **Performance Metrics**: Velocity tracking, quality indicators, stakeholder satisfaction

## Success Criteria

The resulting project.instructions.md should enable:

**Developer Productivity:**

- New team members can set up development environment within 2 hours
- Clear guidance on implementing academic business rules and workflows
- Standardized approaches to common development tasks and patterns
- Efficient debugging and troubleshooting procedures

**Quality Assurance:**

- Consistent code quality across all team members and contributions
- Comprehensive testing coverage for academic scenarios and edge cases
- Security and compliance requirements integrated into development workflow
- Performance benchmarks and optimization guidelines

**Project Management:**

- Clear definition of done for features and user stories
- Standardized estimation and planning processes
- Risk identification and mitigation strategies
- Stakeholder communication and feedback integration

**Institutional Readiness:**

- Guidelines for institutional customization and configuration
- Deployment procedures for different university environments
- Training materials and documentation for end-user adoption
- Support procedures for post-deployment operations and maintenance

## Example Sections to Include

### Prerequisites & Setup

```markdown
## Prerequisites

- Node.js 18+ / .NET 6+ / Python 3.9+
- Docker and Docker Compose
- Database (PostgreSQL 13+)
- Git 2.30+
```

### Development Standards

```markdown
## Coding Standards

### Academic Entity Naming

- Employee numbers: 6-character fixed length (empNr)
- Academic ranks: Standardized codes (L, SL, AP, P)
- Extension numbers: Variable length numeric (extNr)
```

### Workflow Implementation

```markdown
## Academic Workflow Development

### Promotion Workflows

1. Rank validation and eligibility checking
2. Committee assignment and review process
3. Approval chain and decision tracking
4. Notification and record updates
```

### Testing Guidelines

```markdown
## Academic Scenario Testing

### Test Data Management

- Use anonymized academic records
- Include edge cases (emeritus status, joint appointments)
- Validate compliance with institutional policies
```
