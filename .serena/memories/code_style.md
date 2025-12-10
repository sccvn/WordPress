# WordPress Coding Standards and Conventions

## PHP Coding Standards

### Naming Conventions

```php
// Functions: lowercase with underscores
function wp_get_current_user() { }
function get_post_meta( $post_id, $key, $single ) { }

// Classes: StudlyCase with WP_ prefix for core classes
class WP_Query { }
class WP_Post { }
class WP_User { }
class WP_Hook { }

// Class methods: lowercase with underscores
public function get_posts() { }
private function parse_query() { }

// Constants: UPPERCASE with underscores
define( 'ABSPATH', __DIR__ . '/' );
define( 'WPINC', 'wp-includes' );

// Variables: lowercase with underscores
$post_id = 123;
$user_data = get_userdata( $user_id );

// Global variables: lowercase with wp_ prefix
global $wp_query;
global $wp_filter;
```

### Spacing and Formatting

```php
// Spaces inside parentheses
if ( $condition ) { }
while ( have_posts() ) { }
function example( $arg1, $arg2 ) { }

// Spaces around operators
$result = $a + $b;
$value = ( $condition ) ? 'yes' : 'no';

// Array syntax (short arrays NOT used in core)
$array = array(
    'key1' => 'value1',
    'key2' => 'value2',
);

// Braces on same line for control structures
if ( $condition ) {
    // code
} elseif ( $other_condition ) {
    // code
} else {
    // code
}

// Yoda conditions (constant on left)
if ( true === $result ) { }
if ( 'value' === $variable ) { }
```

### Indentation

- Use **tabs** for indentation (not spaces)
- Indent 1 tab per level
- Align multi-line arrays and function arguments

### PHPDoc Comments

```php
/**
 * Brief description of the function.
 *
 * Longer description that spans multiple lines
 * if necessary.
 *
 * @since 1.0.0
 * @since 2.0.0 Added the `$option` parameter.
 *
 * @see related_function()
 * @link https://developer.wordpress.org/reference/functions/example/
 *
 * @global WP_Query $wp_query WordPress Query object.
 *
 * @param int    $post_id   Required. Post ID.
 * @param string $meta_key  Optional. Meta key. Default empty.
 * @param bool   $single    Optional. Return single value. Default false.
 * @return mixed|false Meta value(s) or false on failure.
 */
function get_post_meta( $post_id, $meta_key = '', $single = false ) {
    // ...
}
```

### Class Documentation

```php
/**
 * Core class used to implement action and filter hook functionality.
 *
 * @since 4.7.0
 *
 * @see Iterator
 * @see ArrayAccess
 */
#[AllowDynamicProperties]
final class WP_Hook implements Iterator, ArrayAccess {

    /**
     * Hook callbacks.
     *
     * @since 4.7.0
     * @var array
     */
    public $callbacks = array();

    /**
     * Adds a callback function to a filter hook.
     *
     * @since 4.7.0
     *
     * @param string   $hook_name     The name of the filter.
     * @param callable $callback      The callback to run.
     * @param int      $priority      Order of execution.
     * @param int      $accepted_args Number of arguments.
     */
    public function add_filter( $hook_name, $callback, $priority, $accepted_args ) {
        // ...
    }
}
```

### File Headers

```php
<?php
/**
 * Plugin API: WP_Hook class
 *
 * @package WordPress
 * @subpackage Plugin
 * @since 4.7.0
 */
```

## Security Practices

### Data Sanitization
```php
// Sanitize input
sanitize_text_field( $input );
sanitize_email( $email );
sanitize_title( $title );
absint( $number );
wp_kses( $content, $allowed_html );

// Validate input
is_email( $email );
wp_validate_boolean( $value );
```

### Data Escaping
```php
// Always escape output
esc_html( $text );
esc_attr( $attribute );
esc_url( $url );
esc_js( $javascript );
wp_kses_post( $content );
```

### Nonces (CSRF Protection)
```php
// Create nonce
wp_nonce_field( 'action_name', 'nonce_name' );
wp_create_nonce( 'action_name' );

// Verify nonce
wp_verify_nonce( $_POST['nonce_name'], 'action_name' );
check_admin_referer( 'action_name', 'nonce_name' );
```

### Capability Checks
```php
if ( ! current_user_can( 'edit_posts' ) ) {
    wp_die( 'Unauthorized access' );
}
```

### Database Queries
```php
// ALWAYS use prepare() for user input
$wpdb->prepare(
    "SELECT * FROM {$wpdb->posts} WHERE ID = %d AND post_status = %s",
    $post_id,
    'publish'
);
```

## Internationalization (i18n)

```php
// Simple strings
__( 'Hello World', 'textdomain' );
_e( 'Hello World', 'textdomain' ); // Echo

// Strings with context
_x( 'Post', 'noun', 'textdomain' );
_ex( 'Post', 'noun', 'textdomain' );

// Plurals
_n( '%s item', '%s items', $count, 'textdomain' );

// With placeholder
sprintf( __( 'Hello %s', 'textdomain' ), $name );
```

## Deprecation Pattern

```php
/**
 * @since 1.0.0
 * @deprecated 3.0.0 Use new_function() instead.
 * @see new_function()
 */
function old_function() {
    _deprecated_function( __FUNCTION__, '3.0.0', 'new_function()' );
    return new_function();
}
```
