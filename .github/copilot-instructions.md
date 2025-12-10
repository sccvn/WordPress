# WordPress Multi-Agent System - Global Instructions

## Overview

This multi-agent system is designed to support the complete software development lifecycle for the WordPress codebase, from architecture extraction to implementation, testing, and review.

## System Model: HIVE (Hierarchical Multi-Agent)

```
                    ┌─────────────────────────┐
                    │    HIVE COORDINATOR     │
                    │  (Orchestration Layer)  │
                    └───────────┬─────────────┘
                                │
        ┌───────────────────────┼───────────────────────┐
        │                       │                       │
        ▼                       ▼                       ▼
┌───────────────┐     ┌─────────────────┐     ┌────────────────┐
│  ARCHITECTURE │     │    PLANNING     │     │ IMPLEMENTATION │
│    DIVISION   │     │    DIVISION     │     │    DIVISION    │
└───────┬───────┘     └────────┬────────┘     └───────┬────────┘
        │                      │                      │
        ▼                      ▼                      ▼
┌───────────────┐     ┌─────────────────┐     ┌────────────────┐
│   TESTING     │     │     REVIEW      │     │  DOCUMENTATION │
│   DIVISION    │     │    DIVISION     │     │    DIVISION    │
└───────────────┘     └─────────────────┘     └────────────────┘
```

## Topology: Hierarchical with Star Coordination

Each division operates as a Star topology with a Lead agent coordinating Senior/Junior agents.

## Agent Naming Convention

```
<role>-<domain>.agent.md

Examples:
- lead-Solution_Architect.agent.md
- senior-Software_Engineer.agent.md
- senior-Automation_Tester.agent.md
```

## Division Structure

### 1. Architecture Division
Responsible for extracting, documenting, and visualizing system architecture.

**Agents:**
- `lead-Solution_Architect.agent.md` - Division lead
- `senior-Software_Architect.agent.md` - Low-level architecture extraction
- `senior-Technical_Writer.agent.md` - Documentation with UML diagrams

**Outputs:**
- C4 Component & Container Diagrams (PlantUML)
- Sequence Diagrams
- Class Diagrams
- ERD Diagrams
- Communication Diagrams
- State Machine Diagrams
- Design Pattern Documentation
- Algorithm Documentation

### 2. Planning Division
Responsible for feature planning, requirements analysis, and design specifications.

**Agents:**
- `lead-Business_Analyst.agent.md` - Division lead
- `senior-Business_Analyst.agent.md` - BDD requirements
- `senior-Solution_Architect.agent.md` - High-level design

**Outputs:**
- Feature Requirements (BDD format)
- User Stories with Acceptance Criteria
- High-Level Design Documents
- Low-Level Design Documents
- SOLID-compliant Architecture

### 3. Implementation Division
Responsible for code implementation following designs and standards.

**Agents:**
- `lead-Software_Engineer.agent.md` - Division lead
- `senior-Backend_Developer.agent.md` - PHP/WordPress core
- `senior-Frontend_Developer.agent.md` - JavaScript/React

**Outputs:**
- Implementation following WordPress Coding Standards
- TDD-driven code
- Integration with existing codebase

### 4. Testing Division
Responsible for all testing activities.

**Agents:**
- `lead-Automation_Tester.agent.md` - Division lead
- `senior-Automation_Tester.agent.md` - Unit/Integration tests
- `senior-Manual_Tester.agent.md` - Functional testing
- `senior-Performance_Tester.agent.md` - Load testing

**Outputs:**
- PHPUnit Test Suites
- Integration Tests
- API Tests
- Load Test Scripts (K6/JMeter)
- Test Reports

### 5. Review Division
Responsible for code quality, security, and compliance.

**Agents:**
- `lead-Code_Reviewer.agent.md` - Division lead
- `senior-Security_Analyst.agent.md` - Security review (Snyk)
- `senior-Quality_Engineer.agent.md` - SonarQube compliance

**Outputs:**
- Code Review Reports
- Security Scan Reports
- Quality Gate Reports
- Remediation Recommendations

## Workflow Orchestration

### Standard Feature Development Flow

```
1. PLANNING PHASE
   └─→ Business_Analyst: Requirements (BDD)
       └─→ Solution_Architect: High-Level Design
           └─→ Software_Architect: Low-Level Design

2. IMPLEMENTATION PHASE
   └─→ Software_Engineer: Code Implementation (TDD)
       └─→ Unit Tests: Write failing tests first
           └─→ Implementation: Make tests pass

3. REVIEW PHASE
   └─→ Code_Reviewer: Style & Quality Review
       └─→ Security_Analyst: Snyk Security Scan
           └─→ Quality_Engineer: SonarQube Analysis

4. TESTING PHASE
   └─→ Automation_Tester: Integration Tests
       └─→ Manual_Tester: Functional Validation
           └─→ Performance_Tester: Load Testing
```

### Architecture Extraction Flow

```
1. DISCOVERY PHASE
   └─→ Solution_Architect: Codebase Analysis
       └─→ Software_Architect: Pattern Identification
           └─→ Technical_Writer: Documentation

2. DIAGRAMMING PHASE
   └─→ C4 Model Diagrams
   └─→ Sequence Diagrams
   └─→ Class Diagrams
   └─→ ERD Diagrams
   └─→ State Machine Diagrams
```

## Communication Protocol

### Agent-to-Agent Communication

1. **Task Delegation**: Coordinator → Division Lead → Agent
2. **Status Reporting**: Agent → Division Lead → Coordinator
3. **Artifact Sharing**: Via shared memory/context
4. **Escalation**: Agent → Division Lead → Coordinator

### Memory Files (Serena Integration)

Agents should read/write to these memory files:
- `project_overview.md` - Project context
- `architecture.md` - Architecture decisions
- `code_style.md` - Coding standards
- `task_completion_checklist.md` - Quality gates

## Quality Standards

### Code Quality
- WordPress Coding Standards (PHPCS)
- SonarQube Quality Gate: Pass
- Security Scan (Snyk): No critical/high vulnerabilities

### Documentation Quality
- PlantUML diagrams must be valid
- All public APIs documented
- Architecture Decision Records (ADRs) maintained

### Testing Quality
- Unit Test Coverage: ≥80%
- Integration Tests: All critical paths
- Performance: Meet NFR thresholds

## Agent Invocation

To invoke a specific agent, use the `@agent` syntax:

```
@lead-Solution_Architect Extract the architecture of wp-includes/rest-api
@senior-Automation_Tester Create PHPUnit tests for WP_Query class
@senior-Security_Analyst Run security scan on authentication module
```

## Templates

Feature planning templates are in `.github/prompts/`:
- `feature-planning.prompt.md`
- `bdd-requirements.prompt.md`
- `architecture-design.prompt.md`

## References

- WordPress Coding Standards: https://developer.wordpress.org/coding-standards/
- PHPUnit Documentation: https://phpunit.de/
- PlantUML Reference: https://plantuml.com/
- C4 Model: https://c4model.com/
