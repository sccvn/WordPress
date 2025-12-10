# Feature Planning Prompt

Use this prompt to plan new features with BDD requirements and SOLID design principles.

## Input Template

```
FEATURE: [Feature Name]

## User Story
As a [role]
I want [capability]
So that [benefit]

## Acceptance Criteria
- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

## Context
- Related components: [list]
- Dependencies: [list]
- Constraints: [list]

## Non-Functional Requirements
- Performance: [requirements]
- Security: [requirements]
- Accessibility: [requirements]
```

## Expected Output

### 1. BDD Scenarios (Gherkin)
```gherkin
Feature: [Feature Name]
  As a [role]
  I want [capability]
  So that [benefit]

  Background:
    Given [common precondition]

  Scenario: [Happy path scenario]
    Given [context]
    When [action]
    Then [expected outcome]

  Scenario: [Error scenario]
    Given [context]
    When [invalid action]
    Then [error handling]
```

### 2. High-Level Design
- Component diagram
- Data flow
- Integration points
- API contracts

### 3. Low-Level Design
- Class structure
- Method signatures
- Database schema changes
- Hook integrations

### 4. Implementation Plan
- Tasks breakdown
- Dependencies
- Estimated effort
- Risk assessment

## Usage

```
@lead-Business_Analyst Plan feature using template:
FEATURE: Custom Post Type Registration API

## User Story
As a plugin developer
I want a simplified API to register custom post types
So that I can quickly add content types without boilerplate code

## Acceptance Criteria
- [ ] Single function call to register CPT
- [ ] Automatic label generation
- [ ] REST API support by default
- [ ] Gutenberg block support
```

## Division Workflow

1. **@lead-Business_Analyst** - Creates BDD requirements
2. **@senior-Business_Analyst** - Details Gherkin scenarios
3. **@lead-Solution_Architect** - Creates high-level design
4. **@senior-Software_Architect** - Details low-level design
5. **@senior-Technical_Writer** - Documents with diagrams
