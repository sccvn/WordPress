# HIVE Multi-Agent System Instructions

## Overview

This document provides instructions for using the HIVE (Hierarchical Multi-Agent) system in the WordPress codebase.

## System Architecture

```
                    ┌─────────────────────────┐
                    │    HIVE COORDINATOR     │
                    │  hive-coordinator.agent │
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
│   TESTING     │     │     REVIEW      │     │     (cont.)    │
│   DIVISION    │     │    DIVISION     │     └────────────────┘
└───────────────┘     └─────────────────┘
```

## Agent Invocation

### Using the `@agent` Syntax

Invoke any agent by using the `@` symbol followed by the agent name:

```
@lead-Solution_Architect [task description]
@senior-Automation_Tester [task description]
```

### Division Leads

| Division | Lead Agent | Responsibilities |
|----------|-----------|------------------|
| Architecture | `@lead-Solution_Architect` | Architecture extraction, C4 diagrams |
| Planning | `@lead-Business_Analyst` | BDD requirements, feature planning |
| Implementation | `@lead-Software_Engineer` | TDD implementation coordination |
| Testing | `@lead-Automation_Tester` | Test strategy, test execution |
| Review | `@lead-Code_Reviewer` | Code review, quality gates |

### Senior Agents

| Division | Agent | Specialization |
|----------|-------|----------------|
| Architecture | `@senior-Software_Architect` | Design patterns, algorithms |
| Architecture | `@senior-Technical_Writer` | PlantUML diagrams, documentation |
| Planning | `@senior-Business_Analyst` | Gherkin scenarios, acceptance criteria |
| Implementation | `@senior-Backend_Developer` | PHP, WordPress core |
| Implementation | `@senior-Frontend_Developer` | JavaScript, React, Blocks |
| Testing | `@senior-Automation_Tester` | PHPUnit, integration tests |
| Testing | `@senior-Manual_Tester` | Functional testing |
| Testing | `@senior-Performance_Tester` | K6, JMeter load tests |
| Review | `@senior-Security_Analyst` | Snyk, security review |
| Review | `@senior-Quality_Engineer` | SonarQube, quality metrics |

## Common Workflows

### 1. Architecture Extraction

```
# Extract component architecture
@lead-Solution_Architect Extract the architecture of wp-includes/rest-api/

# Document design patterns
@senior-Software_Architect Identify design patterns in WP_Query class

# Create PlantUML diagrams
@senior-Technical_Writer Create C4 component diagram for REST API
```

### 2. Feature Planning

```
# Plan new feature
@lead-Business_Analyst Plan feature: Enhanced block patterns API

# Create BDD requirements
@senior-Business_Analyst Write Gherkin scenarios for block patterns
```

### 3. TDD Implementation

```
# Implement with TDD
@lead-Software_Engineer Implement enhanced block patterns API using TDD

# Backend implementation
@senior-Backend_Developer Create PHP classes for block pattern registration

# Frontend implementation
@senior-Frontend_Developer Create React components for block pattern UI
```

### 4. Testing

```
# Create test strategy
@lead-Automation_Tester Create test plan for block patterns API

# Write unit tests
@senior-Automation_Tester Create PHPUnit tests for WP_Block_Patterns

# Performance testing
@senior-Performance_Tester Create K6 load test for patterns endpoint
```

### 5. Code Review

```
# Request review
@lead-Code_Reviewer Review PR #1234

# Security scan
@senior-Security_Analyst Run Snyk scan on authentication module

# Quality check
@senior-Quality_Engineer Run SonarQube analysis on wp-includes
```

## Prompt Templates

Use the prompt templates in `.github/prompts/` for structured task input:

| Prompt | Usage |
|--------|-------|
| `feature-planning.prompt.md` | Plan new features with BDD |
| `bdd-requirements.prompt.md` | Create Gherkin scenarios |
| `architecture-extraction.prompt.md` | Extract and document architecture |
| `tdd-implementation.prompt.md` | Implement features with TDD |
| `code-review.prompt.md` | Comprehensive code reviews |

## Quality Standards

### Code Quality
- WordPress Coding Standards (PHPCS)
- SonarQube Quality Gate: Pass
- Test Coverage: ≥ 80%
- No critical/high Snyk vulnerabilities

### Documentation
- PlantUML diagrams must be valid
- PHPDoc on all public APIs
- Changelog entries for changes

### Testing
- PHPUnit for unit/integration tests
- K6/JMeter for performance tests
- BDD scenarios for acceptance tests

## Best Practices

### 1. Start with Planning
Always begin with `@lead-Business_Analyst` to create proper requirements before implementation.

### 2. Use Proper Task Delegation
Let lead agents coordinate their teams - they know which senior agents to involve.

### 3. Follow TDD
Write tests first with `@senior-Automation_Tester`, then implement with backend/frontend developers.

### 4. Always Review
Every change should go through `@lead-Code_Reviewer` and automated scans.

### 5. Document Architecture
Use `@senior-Technical_Writer` to maintain up-to-date architecture documentation.

## Memory Integration

The system uses Serena memory files for context:

| Memory | Purpose |
|--------|---------|
| `project_overview.md` | Project context |
| `architecture.md` | Architecture decisions |
| `code_style.md` | Coding standards |
| `hook_system.md` | WordPress hooks |
| `key_classes.md` | Important classes |
| `rest_api.md` | REST API reference |
| `task_completion_checklist.md` | Quality gates |
| `suggested_commands.md` | CLI commands |

## Troubleshooting

### Agent Not Responding
1. Check agent file exists in `.github/agents/`
2. Verify agent name spelling
3. Check if tools are available to agent

### Quality Gate Failures
1. Run PHPCS locally
2. Check test coverage
3. Review Snyk/SonarQube reports

### Integration Issues
1. Review hook documentation
2. Check Serena memory files
3. Verify WordPress version compatibility
