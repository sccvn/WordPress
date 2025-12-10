# BDD Requirements Prompt

Use this prompt to create comprehensive BDD (Behavior-Driven Development) requirements.

## Gherkin Syntax Reference

```gherkin
Feature: [Feature title]
  [Optional feature description]
  As a [role]
  I want [capability]  
  So that [benefit]

  Background:
    # Steps run before each scenario
    Given [common precondition]
    And [another precondition]

  @tag1 @tag2
  Scenario: [Scenario title]
    Given [initial context]
      And [additional context]
    When [action is performed]
      And [additional action]
    Then [expected outcome]
      And [additional outcome]
      But [exclusion outcome]

  Scenario Outline: [Parameterized scenario]
    Given [context with <variable>]
    When [action with <input>]
    Then [outcome with <expected>]

    Examples:
      | variable | input | expected |
      | value1   | in1   | out1     |
      | value2   | in2   | out2     |

  Rule: [Business rule]
    
    Scenario: [Scenario under this rule]
      Given [context]
      When [action]
      Then [outcome]
```

## Input Template

```
REQUIREMENT: [Requirement Name]

## Business Context
[Describe the business need and why this feature is valuable]

## User Roles
- [Role 1]: [Description]
- [Role 2]: [Description]

## Acceptance Criteria
1. [Criterion 1]
2. [Criterion 2]
3. [Criterion 3]

## Edge Cases
- [Edge case 1]
- [Edge case 2]

## Error Scenarios
- [Error scenario 1]
- [Error scenario 2]
```

## Expected Output

### Feature File Structure
```gherkin
@feature-tag
Feature: [Feature Name]
  [Feature description explaining the business value]

  As a [primary user role]
  I want [the capability]
  So that [the business benefit]

  Background:
    Given the WordPress site is running
    And I am logged in as a [role]

  # Happy Path Scenarios
  @happy-path @smoke
  Scenario: Successfully [main action]
    Given [initial state]
    When I [perform action]
    Then [expected result]

  # Validation Scenarios
  @validation
  Scenario: Validate required fields
    Given I am on the [page]
    When I submit without [required field]
    Then I should see error "[error message]"

  # Permission Scenarios
  @security @permissions
  Scenario: [Role] cannot [restricted action]
    Given I am logged in as a [limited role]
    When I attempt to [restricted action]
    Then I should see "Permission denied"

  # Edge Cases
  @edge-case
  Scenario Outline: Handle [edge case type]
    Given [context]
    When I input "<value>"
    Then I should see "<result>"

    Examples:
      | value          | result           |
      | [valid]        | [success]        |
      | [boundary]     | [boundary result]|
      | [invalid]      | [error]          |

  # Error Scenarios
  @error-handling
  Scenario: Handle [error condition]
    Given [error precondition]
    When [action that triggers error]
    Then I should see error "[user-friendly message]"
    And the error should be logged
```

## WordPress-Specific BDD Examples

### Post Operations
```gherkin
Feature: Post Management
  As a content editor
  I want to manage blog posts
  So that I can publish content for readers

  Background:
    Given the WordPress site is configured
    And I am logged in as an editor

  @posts @crud
  Scenario: Create a new post
    Given I am on the "Add New Post" page
    When I enter "Test Post" as the title
    And I enter "Post content here" in the editor
    And I click "Publish"
    Then the post should be published
    And I should see "Post published" message
    And the post should be visible on the frontend

  @posts @draft
  Scenario: Save post as draft
    Given I am creating a new post
    When I enter "Draft Post" as the title
    And I click "Save Draft"
    Then the post status should be "draft"
    And the post should not be visible on frontend

  @posts @permissions
  Scenario: Contributor cannot publish
    Given I am logged in as a contributor
    When I create a post
    Then I should not see "Publish" button
    And I should see "Submit for Review" button
```

### REST API Operations
```gherkin
Feature: REST API Post Endpoints
  As an API consumer
  I want to interact with posts via REST API
  So that I can build applications using WordPress content

  @api @authentication
  Scenario: Unauthenticated user can read public posts
    Given I am not authenticated
    When I send GET request to "/wp-json/wp/v2/posts"
    Then the response status should be 200
    And the response should contain posts
    And no draft posts should be included

  @api @authentication
  Scenario: Authenticated user can create posts
    Given I am authenticated as an editor
    When I send POST request to "/wp-json/wp/v2/posts" with:
      | title   | content        | status  |
      | API Post | Created via API | publish |
    Then the response status should be 201
    And the response should contain the new post ID

  @api @validation
  Scenario Outline: Validate post creation
    Given I am authenticated as an editor
    When I send POST request to "/wp-json/wp/v2/posts" with:
      | title   | content   | status   |
      | <title> | <content> | <status> |
    Then the response status should be <code>

    Examples:
      | title     | content | status  | code |
      |           | text    | draft   | 400  |
      | Title     | text    | publish | 201  |
      | Title     | text    | invalid | 400  |
```

## Usage

```
@senior-Business_Analyst Create BDD requirements for:

REQUIREMENT: Custom Post Type REST API

## Business Context
Plugin developers need REST API endpoints automatically 
created when registering custom post types.

## User Roles
- Plugin Developer: Creates CPT via code
- API Consumer: Accesses CPT via REST

## Acceptance Criteria
1. REST endpoints created for CPT automatically
2. Same permissions model as core post types
3. Customizable fields exposed in REST

## Edge Cases
- CPT with special characters in name
- CPT with hierarchical structure

## Error Scenarios
- Accessing private CPT without auth
- Invalid field names
```
