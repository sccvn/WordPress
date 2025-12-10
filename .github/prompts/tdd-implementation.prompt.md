# TDD Implementation Prompt

Use this prompt to implement features following Test-Driven Development methodology.

## TDD Cycle

```
     ┌───────────────────────────────────────────┐
     │           TDD RED-GREEN-REFACTOR          │
     ├───────────────────────────────────────────┤
     │                                           │
     │     ┌─────────┐                           │
     │     │  RED    │ Write failing test        │
     │     └────┬────┘                           │
     │          │                                │
     │          ▼                                │
     │     ┌─────────┐                           │
     │     │ GREEN   │ Write minimal code        │
     │     └────┬────┘   to pass test            │
     │          │                                │
     │          ▼                                │
     │     ┌─────────┐                           │
     │     │REFACTOR │ Improve code              │
     │     └────┬────┘   keeping tests green     │
     │          │                                │
     │          └──────────┐                     │
     │                     ▼                     │
     │              Next Feature                 │
     │                                           │
     └───────────────────────────────────────────┘
```

## Input Template

```
IMPLEMENTATION: [Feature Name]

## Requirements
[From BDD scenarios or acceptance criteria]

## Existing Code Context
- Files: [relevant files]
- Classes: [related classes]
- Hooks: [related hooks]

## Test Strategy
- Unit tests: [what to test]
- Integration tests: [what to test]

## Constraints
- [Constraint 1]
- [Constraint 2]
```

## Expected Output

### Step 1: RED - Write Failing Tests

```php
<?php
/**
 * Tests for [Feature]
 *
 * @group feature-name
 */
class Test_Feature extends WP_UnitTestCase {

    /**
     * Test [basic functionality].
     *
     * @covers ::[function_name]
     */
    public function test_basic_functionality() {
        // Arrange
        $input = 'test input';
        
        // Act
        $result = feature_function( $input );
        
        // Assert
        $this->assertSame( 'expected output', $result );
    }

    /**
     * Test [edge case].
     */
    public function test_edge_case() {
        // This test should fail initially
        $result = feature_function( null );
        
        $this->assertFalse( $result );
    }

    /**
     * Test [error handling].
     */
    public function test_error_handling() {
        // This test should fail initially
        $result = feature_function( 'invalid' );
        
        $this->assertWPError( $result );
    }
}
```

### Step 2: GREEN - Minimal Implementation

```php
<?php
/**
 * [Feature] implementation.
 *
 * @package WordPress
 * @subpackage [Subpackage]
 */

/**
 * [Function description].
 *
 * @since [version]
 *
 * @param string $input [Parameter description].
 * @return string|false|WP_Error [Return description].
 */
function feature_function( $input ) {
    // Minimal implementation to pass tests
    if ( null === $input ) {
        return false;
    }
    
    if ( 'invalid' === $input ) {
        return new WP_Error( 'invalid_input', 'Input is invalid.' );
    }
    
    return 'expected output';
}
```

### Step 3: REFACTOR - Improve Code

```php
<?php
/**
 * [Feature] implementation - Refactored.
 *
 * @package WordPress
 * @subpackage [Subpackage]
 */

/**
 * [Function description].
 *
 * @since [version]
 *
 * @param string $input [Parameter description].
 * @return string|false|WP_Error [Return description].
 */
function feature_function( $input ) {
    // Validation
    if ( ! is_valid_input( $input ) ) {
        return handle_invalid_input( $input );
    }
    
    // Processing
    $processed = process_input( $input );
    
    /**
     * Filters the feature function result.
     *
     * @since [version]
     *
     * @param string $processed Processed result.
     * @param string $input     Original input.
     */
    return apply_filters( 'feature_function_result', $processed, $input );
}

/**
 * Validates input for feature function.
 *
 * @param mixed $input Input to validate.
 * @return bool Whether input is valid.
 */
function is_valid_input( $input ) {
    return null !== $input && 'invalid' !== $input;
}

/**
 * Handles invalid input.
 *
 * @param mixed $input Invalid input.
 * @return false|WP_Error Error response.
 */
function handle_invalid_input( $input ) {
    if ( null === $input ) {
        return false;
    }
    
    return new WP_Error(
        'invalid_input',
        __( 'Input is invalid.', 'textdomain' )
    );
}

/**
 * Processes valid input.
 *
 * @param string $input Valid input.
 * @return string Processed output.
 */
function process_input( $input ) {
    return 'expected output';
}
```

## TDD Implementation Checklist

### Before Writing Tests
- [ ] Understand requirements completely
- [ ] Identify test cases from BDD scenarios
- [ ] Set up test environment
- [ ] Know WordPress test utilities

### RED Phase
- [ ] Write test for smallest functionality
- [ ] Test describes expected behavior
- [ ] Test fails for the right reason
- [ ] Test is readable and maintainable

### GREEN Phase
- [ ] Write minimal code to pass
- [ ] Don't over-engineer
- [ ] All tests pass
- [ ] No regression in existing tests

### REFACTOR Phase
- [ ] Remove duplication
- [ ] Improve naming
- [ ] Extract methods/classes
- [ ] Add hooks for extensibility
- [ ] All tests still pass

## WordPress TDD Patterns

### Testing Functions with Hooks
```php
<?php
class Test_Feature_With_Hooks extends WP_UnitTestCase {

    public function test_action_fires() {
        $action_fired = false;
        
        add_action( 'feature_action', function() use ( &$action_fired ) {
            $action_fired = true;
        } );
        
        feature_function( 'input' );
        
        $this->assertTrue( $action_fired );
    }

    public function test_filter_modifies_result() {
        add_filter( 'feature_function_result', function( $result ) {
            return $result . ' modified';
        } );
        
        $result = feature_function( 'input' );
        
        $this->assertStringEndsWith( 'modified', $result );
    }
}
```

### Testing Database Operations
```php
<?php
class Test_Feature_Database extends WP_UnitTestCase {

    public function test_creates_record() {
        $id = create_feature_record( array(
            'name' => 'Test',
        ) );
        
        global $wpdb;
        $record = $wpdb->get_row(
            $wpdb->prepare(
                "SELECT * FROM {$wpdb->prefix}features WHERE id = %d",
                $id
            )
        );
        
        $this->assertNotNull( $record );
        $this->assertSame( 'Test', $record->name );
    }
}
```

### Testing REST API Endpoints
```php
<?php
class Test_Feature_REST extends WP_Test_REST_Controller_Testcase {

    public function test_register_routes() {
        $routes = rest_get_server()->get_routes();
        $this->assertArrayHasKey( '/wp/v2/features', $routes );
    }

    public function test_get_items() {
        $request = new WP_REST_Request( 'GET', '/wp/v2/features' );
        $response = rest_get_server()->dispatch( $request );
        
        $this->assertSame( 200, $response->get_status() );
    }
}
```

## Usage

```
@lead-Software_Engineer Implement using TDD:

IMPLEMENTATION: Custom Meta Registration API

## Requirements
- Register custom meta fields with single function
- Support post, term, user meta
- REST API exposure by default
- Type validation

## Existing Code Context
- Files: wp-includes/meta.php
- Classes: WP_Meta_Query
- Hooks: registered_meta

## Test Strategy
- Unit tests: Registration, validation, sanitization
- Integration tests: REST API exposure, querying

## Constraints
- Backward compatible with register_meta()
- Follow existing patterns
```

## Agent Workflow

1. **@lead-Software_Engineer** - Coordinates implementation
2. **@senior-Backend_Developer** - Writes PHP tests and implementation
3. **@senior-Frontend_Developer** - Writes JS tests (if applicable)
4. **@senior-Automation_Tester** - Reviews test coverage
