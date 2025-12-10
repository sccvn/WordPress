# Task Completion Checklist for WordPress Development

## Before Submitting Code

### 1. Code Quality
- [ ] Code follows WordPress Coding Standards
- [ ] No PHP errors or warnings
- [ ] No JavaScript console errors
- [ ] Variables and functions are properly named
- [ ] No hardcoded values (use constants/options)
- [ ] No duplicate code

### 2. Security
- [ ] All user input is sanitized
- [ ] All output is escaped
- [ ] Nonces used for form submissions
- [ ] Capability checks in place
- [ ] Database queries use `$wpdb->prepare()`
- [ ] No direct file access (check `ABSPATH`)

### 3. Documentation
- [ ] PHPDoc comments for all functions/methods
- [ ] `@since` tags with version number
- [ ] `@param` and `@return` documented
- [ ] Inline comments for complex logic
- [ ] Update relevant documentation

### 4. Internationalization
- [ ] All user-facing strings are translatable
- [ ] Using correct i18n functions (`__()`, `_e()`, etc.)
- [ ] Proper text domain specified
- [ ] No HTML in translation strings (when possible)

### 5. Backward Compatibility
- [ ] Check minimum PHP version (7.2.24)
- [ ] Check minimum MySQL version (5.5.5)
- [ ] Use deprecated functions properly
- [ ] Feature detection over version checks

### 6. Performance
- [ ] Database queries are optimized
- [ ] Appropriate use of caching
- [ ] No unnecessary autoloading
- [ ] Scripts/styles only loaded when needed

## Verification Commands

```bash
# Check PHP syntax
php -l path/to/file.php

# Run PHPCS (if available)
./vendor/bin/phpcs --standard=WordPress path/to/file.php

# Check for PHP compatibility
./vendor/bin/phpcs --standard=PHPCompatibilityWP --runtime-set testVersion 7.2- path/to/file.php

# Run PHPUnit tests (from wordpress-develop)
npm run test:php -- --filter=test_name

# Check for JavaScript issues
npm run lint:js

# Validate JSON files
cat file.json | python -m json.tool
```

## Testing Checklist

- [ ] Unit tests pass
- [ ] Integration tests pass
- [ ] Manual testing completed
- [ ] Tested in different browsers
- [ ] Tested with debug mode enabled
- [ ] Tested with common plugins (if applicable)
- [ ] Tested multisite (if applicable)

## Git Commit Guidelines

```bash
# Commit message format
# Component: Brief description

# Examples:
# REST API: Add pagination to posts endpoint
# Query: Fix meta query with multiple clauses
# Editor: Update block alignment controls
# Build/Test Tools: Add PHP 8.3 to test matrix
```

### Commit Message Types
- **Component**: Area of WordPress (REST API, Query, Editor, etc.)
- **Docs**: Documentation changes
- **Build/Test Tools**: Build process or testing
- **Coding Standards**: Code style fixes
- **I18N**: Internationalization

## Pull Request Checklist

- [ ] Linked to Trac ticket (if applicable)
- [ ] Description explains the change
- [ ] Screenshots for UI changes
- [ ] Unit tests included/updated
- [ ] No merge conflicts
- [ ] CI checks pass
