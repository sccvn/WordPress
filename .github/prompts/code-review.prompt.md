# Code Review Prompt

Use this prompt for comprehensive code reviews covering style, security, quality, and performance.

## Input Template

```
CODE REVIEW REQUEST

## PR/Change Information
- PR Number: #[number]
- Author: [name]
- Files Changed: [count]
- Lines: +[added]/-[removed]

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Refactoring
- [ ] Documentation
- [ ] Performance improvement
- [ ] Security fix

## Description
[Brief description of changes]

## Files to Review
- [file1.php]
- [file2.php]

## Focus Areas
- [ ] Code style
- [ ] Security
- [ ] Performance
- [ ] Architecture
- [ ] Testing
- [ ] Documentation
```

## Review Checklist

### WordPress Coding Standards
```
□ Indentation uses tabs (not spaces)
□ Opening braces on same line as statement
□ Space after control structure keywords
□ Yoda conditions used (if ( true === $var ))
□ Proper naming conventions
  - Functions: snake_case with prefix
  - Classes: Title_Case with underscores
  - Constants: UPPERCASE_WITH_UNDERSCORES
□ PHPDoc comments on all functions/classes
□ Meaningful variable names
□ No trailing whitespace
□ Files end with single newline
```

### Security Review
```
□ Input validation
  - sanitize_text_field() for text
  - absint() for integers
  - esc_url_raw() for URLs
  - wp_kses_post() for HTML
□ Output escaping
  - esc_html() for text
  - esc_attr() for attributes
  - esc_url() for URLs
□ Nonce verification
  - wp_nonce_field() in forms
  - wp_verify_nonce() on submit
  - check_ajax_referer() for AJAX
□ Capability checks
  - current_user_can() before actions
□ SQL injection prevention
  - $wpdb->prepare() for all queries
□ No direct file operations without checks
□ No eval() or similar dangerous functions
```

### Performance Review
```
□ No queries in loops
□ Proper use of caching
  - wp_cache_get/set
  - Transients where appropriate
□ Efficient database queries
  - Proper indexing considered
  - Limit results appropriately
□ Lazy loading where possible
□ No blocking operations
□ Asset optimization
  - Scripts/styles enqueued properly
  - Conditional loading
```

### Architecture Review
```
□ Single Responsibility Principle
  - Functions do one thing
  - Classes have one purpose
□ Open/Closed Principle
  - Extensible via hooks
  - Not modifying core behavior
□ Proper use of WordPress patterns
  - Hooks for extensibility
  - Options API for settings
  - Transients for caching
□ No tight coupling
□ Proper separation of concerns
□ Follows existing codebase patterns
```

### Testing Review
```
□ Unit tests included
□ Tests cover happy path
□ Tests cover edge cases
□ Tests cover error conditions
□ Integration tests where needed
□ Test coverage meets threshold (80%)
□ Tests are maintainable
□ No flaky tests
```

### Documentation Review
```
□ PHPDoc for all public functions
  - @since tag present
  - @param documented
  - @return documented
□ Inline comments for complex logic
□ README updated if needed
□ Changelog entry added
□ Migration notes if breaking changes
```

## Review Comment Templates

### Security Issue
```markdown
🔴 **Security Issue**

**Location**: `file.php:123`
**Severity**: Critical/High/Medium/Low

**Issue**: [Description of security vulnerability]

**Current Code**:
```php
// Vulnerable code
$id = $_GET['id'];
$query = "SELECT * FROM posts WHERE ID = $id";
```

**Recommended Fix**:
```php
// Fixed code
$id = absint( $_GET['id'] );
$query = $wpdb->prepare(
    "SELECT * FROM posts WHERE ID = %d",
    $id
);
```

**Reference**: [Link to security documentation]
```

### Code Style Issue
```markdown
⚠️ **Code Style**

**Location**: `file.php:45`

**Issue**: Spacing doesn't follow WordPress standards

**Current**:
```php
if($condition){
```

**Expected**:
```php
if ( $condition ) {
```

**Reference**: [WordPress PHP Coding Standards](https://developer.wordpress.org/coding-standards/wordpress-coding-standards/php/)
```

### Performance Issue
```markdown
🐢 **Performance Concern**

**Location**: `file.php:78-85`

**Issue**: Query executed inside loop (N+1 problem)

**Current Code**:
```php
foreach ( $post_ids as $id ) {
    $meta = get_post_meta( $id, 'key', true ); // Query per iteration
}
```

**Suggested Fix**:
```php
// Bulk fetch all meta at once
$all_meta = get_post_meta_by_ids( $post_ids, 'key' );
foreach ( $post_ids as $id ) {
    $meta = $all_meta[ $id ] ?? null;
}
```
```

### Architecture Suggestion
```markdown
💡 **Architecture Suggestion**

**Location**: `file.php:100-150`

**Observation**: This function has multiple responsibilities

**Current**: Function handles validation, processing, and saving

**Suggestion**: Consider splitting into:
1. `validate_input()` - Input validation
2. `process_data()` - Business logic
3. `save_data()` - Persistence

**Benefits**:
- Easier to test
- More reusable
- Follows SRP
```

### Positive Feedback
```markdown
✅ **Good Practice**

**Location**: `file.php:200`

Great use of WordPress hooks for extensibility! This allows other developers to modify the behavior without editing core code.
```

## Expected Output

```markdown
# Code Review: PR #[NUMBER]

## Summary
- **Status**: ✅ Approved / ❌ Changes Requested / ⏸️ Needs Discussion
- **Reviewed by**: [Reviewer]
- **Date**: [Date]

## Overview
[Brief summary of the review]

## Findings

### 🔴 Critical (Must Fix)
1. [Finding 1]
2. [Finding 2]

### 🟡 Major (Should Fix)
1. [Finding 1]

### 🟢 Minor (Nice to Have)
1. [Finding 1]

### ✅ Good Practices
1. [What was done well]

## Automated Checks
- [ ] PHPCS: Pass/Fail
- [ ] PHPUnit: Pass/Fail
- [ ] Snyk Security: Pass/Fail
- [ ] SonarQube: Pass/Fail

## Recommendations
1. [Recommendation 1]
2. [Recommendation 2]

## Questions
1. [Question about implementation]
```

## Usage

```
@lead-Code_Reviewer Review PR:

CODE REVIEW REQUEST

## PR/Change Information
- PR Number: #1234
- Author: developer
- Files Changed: 5
- Lines: +200/-50

## Type of Change
- [x] New feature

## Description
Adds new REST API endpoint for custom post type management

## Files to Review
- wp-includes/rest-api/endpoints/class-wp-rest-cpt-controller.php
- tests/phpunit/tests/rest-api/test-cpt-controller.php

## Focus Areas
- [x] Security
- [x] Architecture
- [x] Testing
```

## Agent Workflow

1. **@lead-Code_Reviewer** - Coordinates review, final decision
2. **@senior-Security_Analyst** - Security-focused review
3. **@senior-Quality_Engineer** - Quality metrics review
