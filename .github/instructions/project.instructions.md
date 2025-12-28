# Zeus Academia Project Instructions

## Overview

Zeus Academia is an enterprise-scale academic management platform designed for universities and higher education institutions. This system manages faculty lifecycle, academic ranks, tenure status, degrees, and various administrative workflows with comprehensive integration to HR, payroll, registration, and directory systems.

## Table of Contents

1. [Development Environment Setup](#development-environment-setup)
2. [Project Standards & Guidelines](#project-standards--guidelines)
3. [Development Workflow](#development-workflow)
4. [Domain-Specific Guidelines](#domain-specific-guidelines)
5. [Quality Assurance](#quality-assurance)
6. [Architecture & Technical Standards](#architecture--technical-standards)
7. [Team Structure & Collaboration](#team-structure--collaboration)

---

## Development Environment Setup

### Prerequisites

**Required Tools:**
- **.NET 8 SDK** or **Node.js 18+** or **Python 3.11+** (depending on implementation stack)
- **Docker Desktop** 4.20+ and Docker Compose
- **PostgreSQL 15+** or **Azure Cosmos DB** (for production)
- **Git 2.40+**
- **Visual Studio Code** or **Visual Studio 2022** or **JetBrains Rider**
- **Azure CLI** 2.50+ (for cloud deployments)
- **Kubernetes CLI (kubectl)** (for AKS deployments)

**Development Extensions/Tools:**
- **Azure Tools** extension for VS Code
- **REST Client** or **Postman** for API testing
- **Azure Data Studio** or **pgAdmin** for database management
- **Docker** extension for container management
- **GitLens** for enhanced Git integration

### Installation Steps

#### 1. Clone the Repository

```bash
git clone https://github.com/[organization]/zeus-academia.git
cd zeus-academia
```

#### 2. Install Dependencies

**For .NET Implementation:**
```bash
dotnet restore
dotnet tool restore
```

**For Node.js Implementation:**
```bash
npm install
# or
yarn install
```

**For Python Implementation:**
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
pip install -r requirements.txt
```

#### 3. Database Setup

**Local Development with Docker:**
```bash
docker-compose up -d postgres
```

**Run Migrations:**
```bash
# .NET
dotnet ef database update

# Node.js (TypeORM)
npm run migration:run

# Python (Alembic)
alembic upgrade head
```

#### 4. Configuration

Create environment configuration files:

**.env.local** (for local development):
```env
# Database
DATABASE_URL=postgresql://zeus_user:zeus_pass@localhost:5432/zeus_academia
COSMOS_DB_ENDPOINT=https://localhost:8081  # For local Cosmos emulator

# Azure Services
AZURE_TENANT_ID=your-tenant-id
AZURE_CLIENT_ID=your-client-id
AZURE_KEY_VAULT_URL=https://your-keyvault.vault.azure.net/

# AI Services
AZURE_OPENAI_ENDPOINT=https://your-openai.openai.azure.com/
AZURE_OPENAI_API_KEY=your-api-key
AZURE_OPENAI_DEPLOYMENT=gpt-4.1

# Service Bus
AZURE_SERVICE_BUS_CONNECTION=Endpoint=sb://...

# Application
APP_ENV=development
LOG_LEVEL=debug
API_PORT=5000
```

#### 5. Verification

Run verification tests:

```bash
# .NET
dotnet test --filter Category=Smoke

# Node.js
npm run test:smoke

# Python
pytest tests/smoke/
```

**Verify Services:**
```bash
# Check database connectivity
psql -h localhost -U zeus_user -d zeus_academia -c "SELECT version();"

# Check API health
curl http://localhost:5000/health

# Check Docker containers
docker ps
```

### Troubleshooting

**Common Issues:**

1. **Port Conflicts:**
   - PostgreSQL default port 5432 - check with `netstat -an | grep 5432`
   - API default port 5000 - modify in configuration if needed

2. **Database Connection Failures:**
   - Ensure Docker containers are running: `docker-compose ps`
   - Check connection string format and credentials
   - Verify network connectivity: `docker network ls`

3. **Migration Errors:**
   - Reset database: `docker-compose down -v && docker-compose up -d`
   - Check migration history table
   - Ensure no schema drift between environments

4. **Azure Service Authentication:**
   - Use Azure CLI login: `az login`
   - Verify service principal permissions
   - Check Key Vault access policies

---

## Project Standards & Guidelines

### Code Standards

#### Naming Conventions

**Academic Domain Entities:**
- Employee numbers: 6-character fixed length (`empNr`)
- Academic ranks: Standardized codes (`L`, `SL`, `AP`, `P`)
  - `L` = Lecturer
  - `SL` = Senior Lecturer
  - `AP` = Associate Professor
  - `P` = Professor
- Access levels: `LOC` (Local), `NAT` (National), `INT` (International)
- Extension numbers: Numeric identifier (`extNr`)
- Degree codes: Uppercase abbreviations (`PHD`, `MCS`, `BSc`)

**Code Naming Standards:**

```csharp
// C# Example
public class Academic
{
    public string EmpNr { get; set; }  // Fixed 6-char
    public string EmpName { get; set; }  // Variable 15-char max
    public Rank Rank { get; set; }
    public AccessLevel AccessLevel { get; set; }
    public bool IsTenured { get; set; }
    public DateTime? ContractedUntil { get; set; }
}

public enum Rank
{
    L,   // Lecturer
    SL,  // Senior Lecturer
    AP,  // Associate Professor
    P    // Professor
}
```

```typescript
// TypeScript Example
interface Academic {
  empNr: string;      // Fixed 6-char
  empName: string;    // Variable 15-char max
  rank: Rank;
  accessLevel: AccessLevel;
  isTenured: boolean;
  contractedUntil?: Date;
}

enum Rank {
  L = 'L',   // Lecturer
  SL = 'SL', // Senior Lecturer
  AP = 'AP', // Associate Professor
  P = 'P'    // Professor
}
```

#### File Organization

```
src/
├── domain/                    # Domain entities and business logic
│   ├── entities/             # Core domain entities
│   ├── value-objects/        # Immutable value types
│   ├── aggregates/           # Aggregate roots
│   └── services/             # Domain services
├── application/               # Application services and use cases
│   ├── commands/             # Command handlers (CQRS)
│   ├── queries/              # Query handlers (CQRS)
│   ├── events/               # Event handlers
│   └── workflows/            # Orchestration workflows
├── infrastructure/            # External concerns
│   ├── persistence/          # Database implementations
│   ├── messaging/            # Event bus, Service Bus
│   ├── integrations/         # External system integrations
│   └── ai/                   # AI agent implementations
├── api/                       # API layer
│   ├── controllers/          # HTTP endpoints
│   ├── middleware/           # Request/response pipeline
│   └── models/               # DTOs and view models
└── shared/                    # Shared utilities
    ├── exceptions/           # Custom exceptions
    ├── validators/           # Input validation
    └── extensions/           # Helper extensions
```

### Architecture Principles

#### Layered Architecture

1. **Domain Layer** (Core Business Logic)
   - Contains pure business rules and domain entities
   - No dependencies on external frameworks or infrastructure
   - Enforces business invariants and constraints

2. **Application Layer** (Use Cases)
   - Orchestrates domain objects to fulfill use cases
   - Coordinates workflows and processes
   - Handles transaction boundaries

3. **Infrastructure Layer** (Technical Concerns)
   - Database access, file systems, external APIs
   - Framework-specific implementations
   - AI agent integrations and messaging

4. **Presentation Layer** (User Interface)
   - REST APIs, GraphQL endpoints
   - UI components (web, mobile)
   - API documentation and versioning

#### Dependency Injection

All services must be registered with dependency injection container:

```csharp
// C# Example - Program.cs
builder.Services.AddScoped<IAcademicRepository, AcademicRepository>();
builder.Services.AddScoped<IPromotionService, PromotionService>();
builder.Services.AddScoped<IDecisionAgent, DecisionAgent>();
builder.Services.AddTransient<INotificationService, NotificationService>();
builder.Services.AddSingleton<ICacheService, RedisCacheService>();
```

### Database Guidelines

#### ORM Usage

**Entity Framework Core (.NET):**
```csharp
public class AcademicConfiguration : IEntityTypeConfiguration<Academic>
{
    public void Configure(EntityTypeBuilder<Academic> builder)
    {
        builder.HasKey(a => a.EmpNr);
        
        builder.Property(a => a.EmpNr)
            .IsFixedLength()
            .HasMaxLength(6)
            .IsRequired();
        
        builder.Property(a => a.EmpName)
            .HasMaxLength(15)
            .IsRequired();
        
        builder.HasOne(a => a.Rank)
            .WithMany()
            .HasForeignKey(a => a.RankCode);
        
        builder.HasIndex(a => a.EmpName);
    }
}
```

**Migration Strategy:**
- Always use migrations for schema changes
- Never modify the database directly
- Include rollback scripts for all migrations
- Test migrations in development before applying to staging/production

#### Data Access Patterns

**Repository Pattern:**
```csharp
public interface IAcademicRepository
{
    Task<Academic> GetByEmpNrAsync(string empNr);
    Task<IEnumerable<Academic>> GetByRankAsync(Rank rank);
    Task<Academic> CreateAsync(Academic academic);
    Task UpdateAsync(Academic academic);
    Task DeleteAsync(string empNr);
    Task<bool> ExistsAsync(string empNr);
}
```

**Unit of Work Pattern:**
```csharp
public interface IUnitOfWork : IDisposable
{
    IAcademicRepository Academics { get; }
    IPromotionRepository Promotions { get; }
    IDepartmentRepository Departments { get; }
    
    Task<int> SaveChangesAsync(CancellationToken cancellationToken = default);
    Task BeginTransactionAsync();
    Task CommitTransactionAsync();
    Task RollbackTransactionAsync();
}
```

### API Design

#### RESTful Conventions

**Endpoint Structure:**
```
GET    /api/v1/academics              # List all academics
GET    /api/v1/academics/{empNr}      # Get single academic
POST   /api/v1/academics              # Create academic
PUT    /api/v1/academics/{empNr}      # Update academic
DELETE /api/v1/academics/{empNr}      # Delete academic
PATCH  /api/v1/academics/{empNr}      # Partial update

# Nested resources
GET    /api/v1/academics/{empNr}/promotions
POST   /api/v1/academics/{empNr}/promotions
GET    /api/v1/academics/{empNr}/degrees

# Actions (non-CRUD operations)
POST   /api/v1/academics/{empNr}/promote
POST   /api/v1/academics/{empNr}/demote
POST   /api/v1/academics/{empNr}/assign-class
POST   /api/v1/academics/{empNr}/transfer-department
```

#### Response Format

**Success Response (200 OK):**
```json
{
  "data": {
    "empNr": "000123",
    "empName": "Smith J",
    "rank": "SL",
    "accessLevel": "NAT",
    "isTenured": false,
    "contractedUntil": "2028-06-30T00:00:00Z"
  },
  "metadata": {
    "timestamp": "2025-11-19T10:30:00Z",
    "version": "1.0"
  }
}
```

**Error Response (400 Bad Request):**
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Academic does not meet promotion criteria",
    "details": [
      {
        "field": "yearsInRank",
        "message": "Minimum 5 years required for promotion to Senior Lecturer"
      }
    ]
  },
  "metadata": {
    "timestamp": "2025-11-19T10:30:00Z",
    "traceId": "abc123-def456"
  }
}
```

#### Authentication & Authorization

**JWT Bearer Token:**
```http
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**Role-Based Access Control:**
- `Admin` - Full system access
- `DepartmentHead` - Department management, initiate promotions
- `CommitteeMember` - Review and approve promotions
- `Faculty` - View own records, update personal information
- `HR` - Access to employment and payroll integration
- `Registrar` - Course assignments and academic calendar

### Testing Standards

#### Test Organization

```
tests/
├── unit/                      # Isolated component tests
│   ├── domain/               # Business logic tests
│   ├── services/             # Service layer tests
│   └── validators/           # Validation logic tests
├── integration/               # Component integration tests
│   ├── api/                  # API endpoint tests
│   ├── database/             # Data access tests
│   └── messaging/            # Event handling tests
├── e2e/                       # End-to-end scenario tests
│   ├── workflows/            # Complete workflow tests
│   └── user-scenarios/       # User journey tests
└── smoke/                     # Basic health checks
```

#### Test Data Management

**Use Anonymized Data:**
```csharp
public static class TestDataFactory
{
    public static Academic CreateTestAcademic(
        string empNr = "TEST01",
        Rank rank = Rank.L)
    {
        return new Academic
        {
            EmpNr = empNr,
            EmpName = $"Test {empNr}",
            Rank = rank,
            AccessLevel = GetAccessLevelForRank(rank),
            IsTenured = false,
            ContractedUntil = DateTime.UtcNow.AddYears(3)
        };
    }
}
```

**Academic Scenario Testing:**
```csharp
[Theory]
[InlineData(Rank.L, Rank.SL, 5)]  // Lecturer to Senior Lecturer
[InlineData(Rank.SL, Rank.AP, 7)] // Senior Lecturer to Associate Professor
[InlineData(Rank.AP, Rank.P, 10)] // Associate Professor to Professor
public async Task PromotionRequiresMinimumYearsInRank(
    Rank currentRank, 
    Rank targetRank, 
    int minimumYears)
{
    // Arrange
    var academic = CreateAcademicWithYearsInRank(currentRank, minimumYears - 1);
    
    // Act
    var result = await _promotionService.ValidatePromotionEligibilityAsync(
        academic, targetRank);
    
    // Assert
    Assert.False(result.IsEligible);
    Assert.Contains("minimum years in rank", result.FailureReasons);
}
```

**Edge Cases to Test:**
- Emeritus status transitions
- Joint department appointments
- Sabbatical leave impacts on eligibility
- Tenure clock freezes for parental leave
- Administrative appointments and rank equivalencies

### Documentation Standards

#### Code Comments

```csharp
/// <summary>
/// Promotes an Academic from Lecturer to Senior Lecturer rank.
/// </summary>
/// <param name="empNr">Six-character employee number.</param>
/// <param name="promotionRequest">Details of the promotion request including
/// justification, committee assignment, and effective date.</param>
/// <returns>A PromotionResult containing the outcome and any validation messages.</returns>
/// <exception cref="AcademicNotFoundException">Thrown when empNr does not exist.</exception>
/// <exception cref="PromotionEligibilityException">Thrown when academic does not
/// meet promotion criteria (minimum years, degree requirements, etc.).</exception>
/// <remarks>
/// This method triggers the full promotion workflow including:
/// 1. Eligibility validation (years in rank, degree requirements)
/// 2. Committee assignment and review process
/// 3. Approval chain (Department Head → Dean → Provost)
/// 4. Integration with HR and Payroll systems
/// 5. Access level updates (L → SL changes LOC to NAT)
/// </remarks>
public async Task<PromotionResult> PromoteLecturerToSeniorLecturerAsync(
    string empNr, 
    PromotionRequest promotionRequest)
{
    // Implementation
}
```

#### API Documentation

Use OpenAPI/Swagger annotations:

```csharp
[HttpPost("academics/{empNr}/promote")]
[ProducesResponseType(StatusCodes.Status200OK, Type = typeof(PromotionResult))]
[ProducesResponseType(StatusCodes.Status400BadRequest, Type = typeof(ValidationError))]
[ProducesResponseType(StatusCodes.Status404NotFound)]
[SwaggerOperation(
    Summary = "Initiate academic promotion",
    Description = "Initiates the promotion workflow for an academic faculty member. " +
                  "Validates eligibility criteria and assigns to appropriate committee.",
    Tags = new[] { "Promotions" }
)]
public async Task<ActionResult<PromotionResult>> PromoteAcademic(
    [FromRoute] string empNr,
    [FromBody] PromotionRequest request)
{
    // Implementation
}
```

---

## Development Workflow

### Git Workflow

#### Branching Strategy

**Main Branches:**
- `main` - Production-ready code
- `develop` - Integration branch for features
- `staging` - Pre-production testing

**Supporting Branches:**
- `feature/*` - New features (e.g., `feature/promotion-workflow`)
- `bugfix/*` - Bug fixes (e.g., `bugfix/tenure-validation`)
- `hotfix/*` - Urgent production fixes (e.g., `hotfix/security-patch`)
- `release/*` - Release preparation (e.g., `release/v2.1.0`)

#### Commit Conventions

Follow [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting)
- `refactor`: Code refactoring
- `perf`: Performance improvements
- `test`: Test additions or modifications
- `chore`: Build process or auxiliary tool changes

**Examples:**
```
feat(promotions): add AI-powered eligibility validation

Implements GPT-4.1 based validation for promotion eligibility
using natural language processing to analyze academic records
and institutional policies.

Refs: #123
```

```
fix(tenure): correct calculation for sabbatical leave credit

Sabbatical leave was not being properly credited toward tenure
clock. Updated calculation to include approved sabbatical periods.

Fixes: #456
Closes: #457
```

#### Pull Request Process

1. **Create Feature Branch:**
   ```bash
   git checkout -b feature/promotion-ai-validation develop
   ```

2. **Develop and Test:**
   - Write code following project standards
   - Add unit and integration tests
   - Update documentation
   - Ensure all tests pass locally

3. **Submit Pull Request:**
   - Provide clear description of changes
   - Reference related issues/work items
   - Include screenshots/videos for UI changes
   - List breaking changes if any

4. **Code Review Checklist:**
   - [ ] Code follows established standards
   - [ ] All tests pass (unit, integration, e2e)
   - [ ] Documentation updated
   - [ ] No security vulnerabilities
   - [ ] Performance impact assessed
   - [ ] Academic domain rules validated
   - [ ] Accessibility requirements met
   - [ ] Database migrations included and tested

5. **Approval Requirements:**
   - At least 2 approvals for feature branches
   - Senior architect approval for architectural changes
   - Domain expert approval for academic business rules
   - Security team approval for authentication/authorization changes

### Feature Development Lifecycle

#### 1. Requirement Analysis

- Review use case documentation in `.github/requirements/use-cases/`
- Consult ORM model in `.github/requirements/orm/`
- Identify affected systems and integrations
- Assess compliance and security requirements

#### 2. Design Phase

- Create or update architectural decision records (ADRs)
- Design database schema changes
- Define API contracts
- Plan AI agent integration points
- Identify test scenarios

#### 3. Implementation

- Follow TDD (Test-Driven Development) where appropriate
- Implement domain logic first (inner layers)
- Add infrastructure concerns (outer layers)
- Integrate AI agents for decision support
- Update API documentation

#### 4. Testing

- Unit tests for business logic (>80% coverage)
- Integration tests for API endpoints
- E2E tests for critical workflows
- Performance testing for high-volume operations
- Security scanning and vulnerability assessment

#### 5. Deployment

- Create release notes
- Update version numbers (semantic versioning)
- Deploy to staging environment
- Run smoke tests and UAT
- Deploy to production with rollback plan

### Continuous Integration

#### Build Pipeline

```yaml
# Azure DevOps Pipeline Example
trigger:
  branches:
    include:
      - main
      - develop
      - feature/*

stages:
  - stage: Build
    jobs:
      - job: Compile
        steps:
          - task: UseDotNet@2
            inputs:
              version: '8.x'
          - script: dotnet build --configuration Release
          - script: dotnet test --configuration Release --logger trx

  - stage: CodeQuality
    jobs:
      - job: SonarQube
        steps:
          - task: SonarQubePrepare@5
          - script: dotnet build
          - task: SonarQubeAnalyze@5

  - stage: Security
    jobs:
      - job: SecurityScan
        steps:
          - task: dependency-check@1
          - task: CredScan@3

  - stage: Deploy
    condition: and(succeeded(), eq(variables['Build.SourceBranch'], 'refs/heads/main'))
    jobs:
      - deployment: DeployToStaging
        environment: 'staging'
        strategy:
          runOnce:
            deploy:
              steps:
                - task: AzureWebApp@1
                - script: npm run test:smoke
```

### Release Management

#### Versioning Strategy

Follow Semantic Versioning (SemVer): `MAJOR.MINOR.PATCH`

- **MAJOR**: Breaking changes, incompatible API changes
- **MINOR**: New features, backwards-compatible
- **PATCH**: Bug fixes, backwards-compatible

**Examples:**
- `1.0.0` - Initial production release
- `1.1.0` - Add new promotion workflow features
- `1.1.1` - Fix tenure calculation bug
- `2.0.0` - Redesign API with breaking changes

#### Release Notes Template

```markdown
# Zeus Academia v1.2.0 - Release Notes

**Release Date:** 2025-11-19

## 🎉 New Features

- **AI-Powered Promotion Validation**: Automated eligibility checking using GPT-4.1
- **Multi-Agent Workflow**: Committee review coordination with specialized AI agents
- **Enhanced Analytics Dashboard**: Real-time insights into promotion pipeline

## 🐛 Bug Fixes

- Fixed tenure clock calculation for sabbatical leave (#456)
- Corrected access level updates during promotion (#457)
- Resolved notification delivery failures (#458)

## ⚡ Performance Improvements

- Optimized database queries for academic record retrieval (30% faster)
- Implemented Redis caching for frequently accessed data
- Reduced API response times by 40% through parallel processing

## 🔒 Security Updates

- Updated authentication middleware to prevent token replay attacks
- Enhanced audit logging for sensitive operations
- Applied security patches for dependencies

## 📚 Documentation

- Added comprehensive API documentation with examples
- Updated architectural decision records
- Improved onboarding guide for new developers

## ⚠️ Breaking Changes

None in this release.

## 🔄 Migration Guide

No database migrations required.

## 📦 Dependencies

- Updated .NET to 8.0.5
- Upgraded Azure SDK to 1.40.0
- Updated OpenAI client library to 2.5.0

## 👥 Contributors

Special thanks to all contributors who made this release possible.
```

---

## Domain-Specific Guidelines

### Academic Domain Knowledge

#### Higher Education Hierarchy

**Academic Ranks (US System):**
1. **Lecturer (L)** - Teaching-focused position
   - Access Level: LOC (Local/Campus)
   - Typical requirements: Master's degree
   
2. **Senior Lecturer (SL)** - Experienced teaching position
   - Access Level: NAT (National)
   - Typical requirements: 5+ years as Lecturer, demonstrated teaching excellence
   
3. **Associate Professor (AP)** - Tenure-track or tenured
   - Access Level: NAT (National)
   - Typical requirements: PhD, significant research output, teaching excellence
   
4. **Professor (P)** - Senior faculty position
   - Access Level: INT (International)
   - Typical requirements: Sustained research excellence, national recognition, leadership

#### Tenure Process

**Tenure Track Timeline:**
- Year 1-3: Initial appointment period
- Year 3: Mid-tenure review
- Year 6: Tenure decision
- Alternative paths: Sabbatical extensions, parental leave adjustments

**Tenure Criteria:**
- Research productivity and impact
- Teaching effectiveness
- Service contributions
- External recognition
- Alignment with institutional mission

### Business Rule Implementation

#### Promotion Eligibility Rules

```csharp
public class PromotionEligibilityService
{
    public async Task<EligibilityResult> ValidatePromotionAsync(
        Academic academic, 
        Rank targetRank)
    {
        var rules = new List<IEligibilityRule>
        {
            new MinimumYearsInRankRule(),
            new DegreeRequirementRule(),
            new TeachingPerformanceRule(),
            new ResearchOutputRule(),
            new ServiceContributionRule(),
            new InstitutionalPolicyRule()
        };

        var results = new List<RuleResult>();
        
        foreach (var rule in rules)
        {
            var result = await rule.EvaluateAsync(academic, targetRank);
            results.Add(result);
        }

        return new EligibilityResult(results);
    }
}

public class MinimumYearsInRankRule : IEligibilityRule
{
    private static readonly Dictionary<(Rank From, Rank To), int> MinimumYears = new()
    {
        { (Rank.L, Rank.SL), 5 },
        { (Rank.SL, Rank.AP), 7 },
        { (Rank.AP, Rank.P), 10 }
    };

    public Task<RuleResult> EvaluateAsync(Academic academic, Rank targetRank)
    {
        var minimumYears = MinimumYears[(academic.Rank, targetRank)];
        var yearsInRank = CalculateYearsInRank(academic);
        
        if (yearsInRank < minimumYears)
        {
            return Task.FromResult(RuleResult.Failure(
                $"Insufficient years in rank. Required: {minimumYears}, Actual: {yearsInRank}"));
        }
        
        return Task.FromResult(RuleResult.Success());
    }
}
```

### Data Privacy & Compliance

#### FERPA Compliance

**Family Educational Rights and Privacy Act (FERPA):**
- Protect student and faculty educational records
- Restrict access to authorized personnel only
- Maintain audit logs of all record access
- Provide consent mechanisms for data sharing

**Implementation Guidelines:**
```csharp
[Authorize(Roles = "Faculty,DepartmentHead,HR")]
public async Task<Academic> GetAcademicDetailsAsync(string empNr)
{
    // Log access for audit trail
    await _auditService.LogAccessAsync(
        empNr, 
        User.Identity.Name, 
        "Academic Record Access");
    
    // Apply field-level security based on role
    var academic = await _repository.GetByEmpNrAsync(empNr);
    return _dataFilter.ApplySecurityFilter(academic, User);
}
```

#### Data Classification

**Sensitivity Levels:**
1. **Public**: Rank, department, office location
2. **Internal**: Teaching assignments, committee memberships
3. **Confidential**: Salary, tenure status, promotion reviews
4. **Restricted**: Social security numbers, health information

**Encryption Requirements:**
- At rest: AES-256 for confidential and restricted data
- In transit: TLS 1.3 for all communications
- Key management: Azure Key Vault with HSM backing

### Integration Patterns

#### University System Integrations

**HR Integration:**
```csharp
public interface IHRIntegrationService
{
    Task SyncEmploymentDataAsync(string empNr);
    Task UpdatePayrollOnPromotionAsync(string empNr, Rank newRank);
    Task ProcessTerminationAsync(string empNr, DateTime effectiveDate);
}

public class HRIntegrationService : IHRIntegrationService
{
    private readonly IHttpClientFactory _httpFactory;
    private readonly IMessagePublisher _messagePublisher;
    
    public async Task UpdatePayrollOnPromotionAsync(string empNr, Rank newRank)
    {
        // Publish event to Service Bus
        var @event = new AcademicPromotedEvent
        {
            EmpNr = empNr,
            NewRank = newRank,
            EffectiveDate = DateTime.UtcNow,
            SalaryAdjustment = CalculateSalaryAdjustment(newRank)
        };
        
        await _messagePublisher.PublishAsync("hr.promotions", @event);
        
        // Also make synchronous HTTP call for immediate update
        var client = _httpFactory.CreateClient("HRSystem");
        await client.PostAsJsonAsync($"/api/payroll/promotions", @event);
    }
}
```

**Course Registration Integration:**
```csharp
public interface ICourseRegistrationService
{
    Task AssignCourseToAcademicAsync(string empNr, string courseCode);
    Task RemoveCourseAssignmentAsync(string empNr, string courseCode);
    Task GetTeachingScheduleAsync(string empNr, int semester);
}
```

#### Event-Driven Integration

```csharp
public class PromotionEventHandler : IEventHandler<AcademicPromotedEvent>
{
    public async Task HandleAsync(AcademicPromotedEvent @event)
    {
        // Update directory services
        await _directoryService.UpdateAcademicRankAsync(
            @event.EmpNr, 
            @event.NewRank);
        
        // Update access control
        await _accessControlService.UpdatePermissionsAsync(
            @event.EmpNr, 
            GetAccessLevelForRank(@event.NewRank));
        
        // Send notifications
        await _notificationService.NotifyStakeholdersAsync(@event);
        
        // Log for compliance
        await _auditService.LogPromotionAsync(@event);
    }
}
```

### Workflow Implementation

#### AI-Powered Promotion Workflow

```csharp
public class PromotionWorkflowOrchestrator
{
    private readonly IAgent _complianceAgent;
    private readonly IAgent _decisionAgent;
    private readonly IAgent _workflowAgent;
    
    public async Task<PromotionResult> ExecutePromotionWorkflowAsync(
        string empNr, 
        Rank targetRank)
    {
        // 1. Compliance validation
        var complianceResult = await _complianceAgent.ValidateAsync(
            $"Validate promotion eligibility for {empNr} to {targetRank}");
        
        if (!complianceResult.IsValid)
        {
            return PromotionResult.ComplianceFailure(complianceResult.Issues);
        }
        
        // 2. AI-powered decision analysis
        var decisionAnalysis = await _decisionAgent.AnalyzeAsync(
            $"Analyze qualifications for {empNr} promotion to {targetRank}");
        
        // 3. Workflow coordination
        var workflow = await _workflowAgent.OrchestrateAsync(new
        {
            EmpNr = empNr,
            TargetRank = targetRank,
            ComplianceResult = complianceResult,
            DecisionAnalysis = decisionAnalysis
        });
        
        // 4. Execute multi-step workflow
        return await workflow.ExecuteAsync();
    }
}
```

---

## Quality Assurance

### Testing Strategy

#### Test Pyramid

```
         /\
        /E2E\          10% - End-to-End Tests
       /------\
      /Integr-\        20% - Integration Tests
     /----------\
    /   Unit     \     70% - Unit Tests
   /--------------\
```

#### Academic Scenario Testing

**Promotion Workflow Test:**
```csharp
[Fact]
public async Task CompletePromotionWorkflow_LecturerToSeniorLecturer_Success()
{
    // Arrange
    var academic = TestDataFactory.CreateQualifiedLecturer();
    var promotionRequest = new PromotionRequest
    {
        TargetRank = Rank.SL,
        Justification = "Meets all criteria for promotion",
        SupportingDocuments = new[] { "cv.pdf", "teaching-evals.pdf" }
    };
    
    // Act
    var result = await _promotionService.InitiatePromotionAsync(
        academic.EmpNr, 
        promotionRequest);
    
    // Simulate committee approval
    await _promotionService.SubmitCommitteeDecisionAsync(
        result.PromotionId, 
        CommitteeDecision.Approve,
        "Strong teaching record and consistent research output");
    
    // Simulate dean approval
    await _promotionService.SubmitDeanApprovalAsync(
        result.PromotionId, 
        approved: true);
    
    // Assert
    var updatedAcademic = await _academicRepository.GetByEmpNrAsync(academic.EmpNr);
    Assert.Equal(Rank.SL, updatedAcademic.Rank);
    Assert.Equal(AccessLevel.NAT, updatedAcademic.AccessLevel);
    
    // Verify integrations
    _hrIntegrationMock.Verify(x => 
        x.UpdatePayrollOnPromotionAsync(academic.EmpNr, Rank.SL), 
        Times.Once);
    _notificationMock.Verify(x => 
        x.SendPromotionNotificationAsync(It.IsAny<PromotionNotification>()), 
        Times.AtLeastOnce);
}
```

**Edge Case Testing:**
```csharp
[Theory]
[InlineData(Rank.P, Rank.L)] // Cannot demote Professor to Lecturer directly
[InlineData(Rank.L, Rank.P)]  // Cannot promote Lecturer to Professor directly
public async Task InvalidRankTransition_ThrowsException(Rank from, Rank to)
{
    // Arrange
    var academic = TestDataFactory.CreateAcademicWithRank(from);
    
    // Act & Assert
    await Assert.ThrowsAsync<InvalidRankTransitionException>(
        () => _promotionService.InitiatePromotionAsync(academic.EmpNr, to));
}

[Fact]
public async Task AcademicOnSabbatical_TenureClockFrozen()
{
    // Arrange
    var academic = TestDataFactory.CreateTenureTrackAcademic();
    var sabbaticalStart = DateTime.UtcNow;
    var sabbaticalEnd = sabbaticalStart.AddMonths(6);
    
    // Act
    await _academicService.StartSabbaticalAsync(
        academic.EmpNr, 
        sabbaticalStart, 
        sabbaticalEnd);
    
    // Assert
    var tenureStatus = await _tenureService.GetTenureStatusAsync(academic.EmpNr);
    Assert.True(tenureStatus.ClockFrozen);
    Assert.Equal(sabbaticalEnd, tenureStatus.ClockResumeDate);
}
```

### Performance Standards

#### Response Time Requirements

| Operation Type | Target Response Time | Maximum Response Time |
|---------------|---------------------|---------------------|
| Simple query (by ID) | < 100ms | < 500ms |
| List operations (paginated) | < 300ms | < 1s |
| Complex searches | < 500ms | < 2s |
| Promotion workflow initiation | < 1s | < 3s |
| Report generation | < 5s | < 15s |
| Bulk operations | < 10s | < 30s |

#### Scalability Benchmarks

**Load Testing Targets:**
- **Concurrent Users**: Support 1,000+ concurrent users
- **Requests per Second**: Handle 5,000+ RPS
- **Database Queries**: < 50ms for 95th percentile
- **API Throughput**: 10,000+ requests/minute
- **Message Processing**: 1,000+ events/second

**Performance Testing:**
```bash
# Load testing with k6
k6 run --vus 100 --duration 30s performance-tests/api-load-test.js

# Database performance testing
./scripts/db-performance-test.sh --concurrent-queries 50 --duration 60
```

### Security Requirements

#### Authentication

**Azure Active Directory B2C:**
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAdB2C"));
    
    services.AddAuthorization(options =>
    {
        options.AddPolicy("PromotionInitiator", policy =>
            policy.RequireRole("DepartmentHead", "Dean"));
        
        options.AddPolicy("PromotionReviewer", policy =>
            policy.RequireRole("CommitteeMember"));
        
        options.AddPolicy("SystemAdmin", policy =>
            policy.RequireRole("Admin"));
    });
}
```

#### Authorization

**Fine-Grained Access Control:**
```csharp
public class AcademicAuthorizationHandler : 
    AuthorizationHandler<OperationAuthorizationRequirement, Academic>
{
    protected override Task HandleRequirementAsync(
        AuthorizationHandlerContext context,
        OperationAuthorizationRequirement requirement,
        Academic academic)
    {
        var userId = context.User.FindFirst(ClaimTypes.NameIdentifier)?.Value;
        
        // Users can view their own records
        if (requirement.Name == Operations.Read && academic.EmpNr == userId)
        {
            context.Succeed(requirement);
        }
        
        // Department heads can view all in their department
        if (requirement.Name == Operations.Read && 
            context.User.IsInRole("DepartmentHead") &&
            IsSameDepartment(userId, academic.EmpNr))
        {
            context.Succeed(requirement);
        }
        
        return Task.CompletedTask;
    }
}
```

#### Data Encryption

**Sensitive Field Encryption:**
```csharp
[DataEncryption(KeyVaultKeyName = "academic-data-key")]
public class Academic
{
    public string EmpNr { get; set; }
    
    [Encrypted]
    public string SocialSecurityNumber { get; set; }
    
    [Encrypted]
    public decimal Salary { get; set; }
    
    public string EmpName { get; set; }  // Not encrypted
}
```

### Accessibility

**WCAG 2.1 Level AA Compliance:**
- Keyboard navigation support
- Screen reader compatibility
- Color contrast ratios (4.5:1 minimum)
- Alternative text for images
- Form validation with clear error messages
- Responsive design for mobile devices

**Implementation:**
```typescript
// Accessible form component
<form aria-labelledby="promotion-form-title">
  <h2 id="promotion-form-title">Academic Promotion Request</h2>
  
  <label for="empNr">
    Employee Number
    <span aria-label="required" className="required">*</span>
  </label>
  <input 
    id="empNr" 
    name="empNr" 
    required 
    aria-required="true"
    aria-describedby="empNr-help" />
  <p id="empNr-help" className="help-text">
    Enter 6-character employee number
  </p>
  
  <button type="submit" aria-label="Submit promotion request">
    Submit Request
  </button>
</form>
```

### Monitoring & Observability

#### Application Performance Monitoring

**Azure Application Insights:**
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(options =>
    {
        options.ConnectionString = Configuration["ApplicationInsights:ConnectionString"];
        options.EnableAdaptiveSampling = true;
        options.EnableDependencyTrackingTelemetryModule = true;
    });
    
    services.AddSingleton<ITelemetryInitializer, CloudRoleNameInitializer>();
}

public class CloudRoleNameInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        telemetry.Context.Cloud.RoleName = "Zeus-Academia-API";
    }
}
```

**Custom Metrics:**
```csharp
public class PromotionMetrics
{
    private readonly TelemetryClient _telemetry;
    
    public void TrackPromotionInitiated(Rank fromRank, Rank toRank)
    {
        _telemetry.TrackEvent("PromotionInitiated", 
            properties: new Dictionary<string, string>
            {
                { "FromRank", fromRank.ToString() },
                { "ToRank", toRank.ToString() }
            },
            metrics: new Dictionary<string, double>
            {
                { "PromotionCount", 1 }
            });
    }
    
    public void TrackPromotionDuration(TimeSpan duration)
    {
        _telemetry.TrackMetric("PromotionWorkflowDuration", duration.TotalSeconds);
    }
}
```

#### Distributed Tracing

**OpenTelemetry Integration:**
```csharp
services.AddOpenTelemetryTracing(builder =>
{
    builder
        .AddAspNetCoreInstrumentation()
        .AddHttpClientInstrumentation()
        .AddSqlClientInstrumentation()
        .AddAzureMonitorTraceExporter(options =>
        {
            options.ConnectionString = Configuration["ApplicationInsights:ConnectionString"];
        });
});
```

---

## Architecture & Technical Standards

### Cloud-Native Architecture

#### Microservices Design

**Service Boundaries:**

1. **Academic Service**
   - Core academic entity management
   - Rank and access level administration
   - Degree and qualification tracking

2. **Promotion Service**
   - Promotion workflow orchestration
   - Eligibility validation
   - Approval process management

3. **Notification Service**
   - Multi-channel notifications (email, SMS, push)
   - Template management
   - Delivery tracking

4. **Integration Service**
   - HR system integration
   - Payroll synchronization
   - Directory service updates

5. **Analytics Service**
   - Reporting and dashboards
   - Predictive analytics
   - Data warehouse integration

#### Container Architecture

**Dockerfile Example:**
```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src
COPY ["Zeus.Academia.Api/Zeus.Academia.Api.csproj", "Zeus.Academia.Api/"]
RUN dotnet restore "Zeus.Academia.Api/Zeus.Academia.Api.csproj"
COPY . .
WORKDIR "/src/Zeus.Academia.Api"
RUN dotnet build "Zeus.Academia.Api.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "Zeus.Academia.Api.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "Zeus.Academia.Api.dll"]
```

**Docker Compose (Development):**
```yaml
version: '3.8'

services:
  postgres:
    image: postgres:15
    environment:
      POSTGRES_DB: zeus_academia
      POSTGRES_USER: zeus_user
      POSTGRES_PASSWORD: zeus_pass
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

  api:
    build:
      context: .
      dockerfile: Dockerfile
    environment:
      DATABASE_URL: postgresql://zeus_user:zeus_pass@postgres:5432/zeus_academia
      REDIS_URL: redis:6379
    ports:
      - "5000:80"
    depends_on:
      - postgres
      - redis

volumes:
  postgres_data:
```

#### Kubernetes Deployment

**Deployment Manifest:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: zeus-academia-api
  namespace: zeus-production
spec:
  replicas: 3
  selector:
    matchLabels:
      app: zeus-academia-api
  template:
    metadata:
      labels:
        app: zeus-academia-api
    spec:
      containers:
      - name: api
        image: zeusacademia.azurecr.io/api:latest
        ports:
        - containerPort: 80
        env:
        - name: ASPNETCORE_ENVIRONMENT
          value: "Production"
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: zeus-secrets
              key: database-url
        resources:
          requests:
            memory: "256Mi"
            cpu: "250m"
          limits:
            memory: "512Mi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /health/live
            port: 80
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /health/ready
            port: 80
          initialDelaySeconds: 5
          periodSeconds: 5
---
apiVersion: v1
kind: Service
metadata:
  name: zeus-academia-api
  namespace: zeus-production
spec:
  selector:
    app: zeus-academia-api
  ports:
  - port: 80
    targetPort: 80
  type: LoadBalancer
```

### Infrastructure as Code

**Azure Bicep Example:**
```bicep
@description('The name of the Azure region')
param location string = resourceGroup().location

@description('The name of the environment')
param environment string = 'production'

// Azure Kubernetes Service
resource aks 'Microsoft.ContainerService/managedClusters@2023-07-01' = {
  name: 'zeus-academia-aks-${environment}'
  location: location
  identity: {
    type: 'SystemAssigned'
  }
  properties: {
    dnsPrefix: 'zeus-academia'
    agentPoolProfiles: [
      {
        name: 'agentpool'
        count: 3
        vmSize: 'Standard_D4s_v3'
        osType: 'Linux'
        mode: 'System'
      }
    ]
    networkProfile: {
      networkPlugin: 'azure'
      serviceCidr: '10.0.0.0/16'
      dnsServiceIP: '10.0.0.10'
    }
  }
}

// Azure Cosmos DB
resource cosmosDb 'Microsoft.DocumentDB/databaseAccounts@2023-04-15' = {
  name: 'zeus-academia-cosmos-${environment}'
  location: location
  properties: {
    databaseAccountOfferType: 'Standard'
    locations: [
      {
        locationName: location
        failoverPriority: 0
      }
    ]
    consistencyPolicy: {
      defaultConsistencyLevel: 'Session'
    }
  }
}

// Azure Key Vault
resource keyVault 'Microsoft.KeyVault/vaults@2023-02-01' = {
  name: 'zeus-kv-${environment}'
  location: location
  properties: {
    sku: {
      family: 'A'
      name: 'standard'
    }
    tenantId: subscription().tenantId
    accessPolicies: []
    enableRbacAuthorization: true
  }
}

// Application Insights
resource appInsights 'Microsoft.Insights/components@2020-02-02' = {
  name: 'zeus-academia-insights-${environment}'
  location: location
  kind: 'web'
  properties: {
    Application_Type: 'web'
  }
}
```

---

## Team Structure & Collaboration

### Role Definitions

#### Product Owner
- Define product vision and roadmap
- Prioritize backlog based on business value
- Accept completed work (definition of done)
- Stakeholder communication and feedback

#### Software Architect (You!)
- Design system architecture and integration patterns
- Define technical standards and best practices
- Review architectural decisions
- Guide technology selection
- Performance and scalability planning

#### Backend Developers
- Implement business logic and domain services
- Build RESTful APIs
- Database design and optimization
- Integration with external systems
- Unit and integration testing

#### Frontend Developers
- Implement user interfaces
- Integrate with backend APIs
- Ensure accessibility compliance
- Performance optimization
- Cross-browser compatibility

#### AI/ML Engineers
- Design and implement AI agent systems
- Model training and fine-tuning
- Prompt engineering and optimization
- AI performance monitoring
- Integration with Azure OpenAI services

#### DevOps Engineers
- CI/CD pipeline management
- Infrastructure as code
- Kubernetes cluster management
- Monitoring and alerting
- Security and compliance

#### QA Engineers
- Test planning and execution
- Automated test development
- Performance testing
- Security testing
- UAT coordination

### Skill Requirements

**Technical Skills:**
- Proficiency in .NET/Node.js/Python
- Experience with microservices architecture
- Cloud platform expertise (Azure preferred)
- Database design (SQL and NoSQL)
- RESTful API design
- Container orchestration (Kubernetes)
- AI/ML integration experience

**Domain Knowledge:**
- Understanding of higher education processes
- Academic terminology and hierarchies
- Compliance requirements (FERPA, accreditation)
- Faculty lifecycle management
- Institutional policy frameworks

### Onboarding Process

**Week 1: Orientation**
- Company and team introduction
- Development environment setup
- Code repository access
- Documentation review
- Attend standup meetings

**Week 2: Domain Education**
- Academic management system overview
- Review use cases and workflows
- Shadow experienced team members
- Complete domain knowledge training
- Review architectural documentation

**Week 3-4: First Contributions**
- Pair programming sessions
- Complete starter tasks
- Submit first pull request
- Participate in code reviews
- Join technical discussions

**Month 2-3: Growing Autonomy**
- Take ownership of features
- Lead technical discussions
- Mentor new team members
- Contribute to documentation
- Participate in sprint planning

### Communication Standards

**Daily Standups (15 minutes):**
- What I completed yesterday
- What I'm working on today
- Any blockers or impediments

**Sprint Planning (2 hours):**
- Review backlog items
- Estimate effort (story points)
- Commit to sprint goals
- Identify dependencies

**Sprint Review (1 hour):**
- Demo completed work
- Gather stakeholder feedback
- Update product backlog

**Sprint Retrospective (1 hour):**
- What went well
- What could be improved
- Action items for next sprint

**Technical Design Reviews:**
- Scheduled as needed for major features
- Include architects, senior developers
- Document decisions in ADRs
- Review security and performance implications

### Performance Metrics

**Development Velocity:**
- Story points completed per sprint
- Cycle time (work item start to completion)
- Lead time (idea to production)
- Deployment frequency

**Quality Indicators:**
- Code coverage percentage (target: >80%)
- Bug escape rate (found in production)
- Test pass rate
- Code review turnaround time

**System Health:**
- API response times
- Error rates
- System availability (target: 99.9%)
- Customer satisfaction scores

---

## Conclusion

This comprehensive guide provides the foundation for successful development of the Zeus Academia system. By following these standards and practices, the development team will deliver a high-quality, scalable, and maintainable academic management platform.

**Remember:**
- Academic domain knowledge is as important as technical skills
- Security and compliance are non-negotiable
- AI integration should enhance, not replace, human decision-making
- Continuous learning and improvement are essential

**Questions or Issues?**
- Consult the `.github/instructions/` directory for detailed guidelines
- Review use cases in `.github/requirements/use-cases/`
- Reach out to the architecture team for design decisions
- Use internal communication channels for quick questions

**Happy Coding! 🎓**
