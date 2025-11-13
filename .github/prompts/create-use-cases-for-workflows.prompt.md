# Create Use Cases for Academic Management Workflows

## Prompt

Create use cases for the workflows defined in the Zeus Academia system's academic management workflows.

## Context

The Zeus Academia system is an academic management platform that handles faculty lifecycle management for universities and higher education institutions. The system has defined workflows for various academic operations that need to be documented as formal use cases following established standards.

## Source Material

### Workflows to Convert

The following workflows from `.github/requirements/workflows.md` need use cases created:

1. **Promotion Workflows:**

   - Promote a Lecturer to Senior Lecturer
   - Promote a Senior Lecturer to Associate Professor
   - Promote an Associate Professor to Professor

2. **Assignment Workflows:**

   - Assign a Class to an Academic

3. **Demotion Workflows:**

   - Demote a Professor to Associate Professor
   - Demote an Associate Professor to Senior Lecturer
   - Demote a Senior Lecturer to Lecturer

4. **Administrative Workflows:**
   - Remove an Academic from the faculty
   - Transfer an Academic between departments, validating eligibility and updating all related information
   - Update an Academic's personal or professional information
   - Assign administrative roles (e.g., Head of Department) to eligible Academics

### Domain Model Context

Use the Zeus Academia system's domain model from `.github/requirements/orm/` which includes:

**Core Entities:**

- Academic (Faculty members) - identified by employee number
- Ranks: Professor (P), Senior Lecturer (SL), Lecturer (L)
- Employment Status: Tenured OR contracted until date (mutually exclusive)
- Degrees & Universities: Educational qualifications tracking
- Extensions: Phone extensions (unique per academic)
- Access Levels: International (INT), National (NAT), Local (LOC)

**Key Business Rules:**

- Rank hierarchy progression
- Access level mapping by rank
- Unique constraints and mandatory requirements
- Tenure vs contract exclusivity

## Requirements

### Use Case Standards

Follow the use case template and guidelines from `.github/instructions/create-use-case.instructions.md`:

- Use standardized template with all required sections
- Include Primary Actor, Supporting Actors, Stakeholders
- Define clear Goal, Scope, and Level for each use case
- Specify Preconditions and Triggers
- Document Main Success Scenario (7-12 steps typical)
- Include comprehensive Alternate/Exception Flows
- Define Success and Minimal Guarantees in Postconditions
- Document relevant Business Rules
- Add Non-Functional Notes for performance, security, compliance
- Include Mermaid sequence diagrams

### Output Organization

- Create individual use case files for each workflow
- Use naming convention: `use-case-<kebab-case-title>.md`
- Store files in `requirements/use-cases/` directory
- One use case per file with inline Mermaid diagrams

### Actor Identification

Identify appropriate actors for academic workflows:

- Department Head, Dean, Provost (for promotions/demotions)
- HR Administrator, Academic Registry (for administrative tasks)
- Department Scheduler, Registrar (for class assignments)
- Academic (for self-service functions)
- External reviewers, committees (for promotion processes)

### Business Process Considerations

Each use case should address:

- **Approval Hierarchies**: Different levels require different authorities
- **Due Process**: Academic actions often require formal procedures
- **Compliance Requirements**: Legal, policy, and accreditation standards
- **System Integration**: HR, Payroll, Directory, Registration systems
- **Audit Trails**: Complete documentation for personnel actions
- **Notification Processes**: Stakeholder communication requirements

## Success Criteria

The resulting use cases should:

1. **Be Comprehensive**: Cover all identified workflows with complete scenarios
2. **Follow Standards**: Adhere to the established use case template and guidelines
3. **Be Actionable**: Provide clear, testable requirements for development
4. **Address Complexity**: Handle academic domain nuances and edge cases
5. **Ensure Compliance**: Meet legal and procedural requirements
6. **Enable Implementation**: Support system design and development planning

### Quality Checklist

- [ ] All 11 workflows have corresponding use cases
- [ ] Each use case follows the standard template
- [ ] Actors and stakeholders are clearly identified
- [ ] Business rules and constraints are documented
- [ ] Exception handling is comprehensive
- [ ] Mermaid diagrams illustrate key flows
- [ ] Files are properly organized and named

## Additional Considerations

- **Academic Calendar Constraints**: Consider timing restrictions for personnel actions
- **Budget and Resource Allocation**: Include financial approval processes where relevant
- **Performance Evaluation Integration**: Link to existing review and assessment processes
- **Legal and Union Requirements**: Address collective bargaining and employment law constraints
- **System Scalability**: Consider multi-department and multi-campus scenarios
- **Data Privacy**: Address confidential personnel information handling

This comprehensive approach ensures the use cases will serve as solid foundation for system requirements, design, and implementation while maintaining the integrity of academic processes and institutional governance.
