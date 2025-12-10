# WordPress Hook System (Actions & Filters)

## Overview

WordPress's hook system is the foundation of its extensibility. It allows code to be executed at specific points (actions) or data to be modified (filters) without changing core files.

## Actions

Actions are triggered at specific points in execution. They don't return values.

### Adding Actions
```php
// Basic usage
add_action( 'hook_name', 'callback_function' );
add_action( 'hook_name', 'callback_function', $priority );
add_action( 'hook_name', 'callback_function', $priority, $accepted_args );

// Default priority is 10
// Lower number = earlier execution

// Class methods
add_action( 'hook_name', array( $object, 'method_name' ) );
add_action( 'hook_name', array( 'ClassName', 'static_method' ) );

// Anonymous functions
add_action( 'hook_name', function() {
    // code
});
```

### Triggering Actions
```php
do_action( 'hook_name' );
do_action( 'hook_name', $arg1, $arg2 );

// Dynamic hook names
do_action( "save_post_{$post_type}", $post_id, $post );
```

### Removing Actions
```php
remove_action( 'hook_name', 'callback_function' );
remove_action( 'hook_name', 'callback_function', $priority );
remove_all_actions( 'hook_name' );
```

## Filters

Filters modify data and must return a value.

### Adding Filters
```php
add_filter( 'filter_name', 'callback_function' );
add_filter( 'filter_name', 'callback_function', $priority, $accepted_args );

function my_filter( $value, $arg1, $arg2 ) {
    // Modify $value
    return $value;
}
```

### Applying Filters
```php
$value = apply_filters( 'filter_name', $value );
$value = apply_filters( 'filter_name', $value, $arg1, $arg2 );

// Apply to each item in array
$array = apply_filters_ref_array( 'filter_name', array( $value, &$ref ) );
```

### Removing Filters
```php
remove_filter( 'filter_name', 'callback_function' );
remove_filter( 'filter_name', 'callback_function', $priority );
remove_all_filters( 'filter_name' );
```

## Common Action Hooks

### Initialization
```php
'muplugins_loaded'   // After must-use plugins load
'plugins_loaded'     // After all plugins load
'setup_theme'        // Before theme loads
'after_setup_theme'  // After theme loads
'init'               // After WordPress fully loads
'wp_loaded'          // After WordPress and plugins loaded
```

### Admin
```php
'admin_init'         // Admin initialization
'admin_menu'         // Admin menu setup
'admin_enqueue_scripts' // Enqueue admin scripts
'admin_notices'      // Display admin notices
```

### Frontend
```php
'wp_enqueue_scripts' // Enqueue frontend scripts
'wp_head'            // Inside <head>
'wp_footer'          // Before </body>
'wp_loaded'          // WordPress fully loaded
'template_redirect'  // Before template loads
```

### Posts
```php
'save_post'          // After post saved
'save_post_{$post_type}' // After specific post type saved
'delete_post'        // Before post deleted
'wp_trash_post'      // Before post trashed
'transition_post_status' // Post status change
```

### Users
```php
'user_register'      // New user registered
'profile_update'     // User profile updated
'wp_login'           // User logged in
'wp_logout'          // User logged out
```

### REST API
```php
'rest_api_init'      // REST API initialized
'rest_pre_dispatch'  // Before REST dispatch
```

## Common Filter Hooks

### Content
```php
'the_content'        // Post content
'the_title'          // Post title
'the_excerpt'        // Post excerpt
'comment_text'       // Comment text
```

### Queries
```php
'posts_where'        // WHERE clause
'posts_join'         // JOIN clause
'posts_orderby'      // ORDER BY clause
'pre_get_posts'      // Modify query before execution
```

### URLs
```php
'home_url'           // Home URL
'site_url'           // Site URL
'admin_url'          // Admin URL
'post_link'          // Post permalink
```

### Admin
```php
'manage_posts_columns'     // Post list columns
'manage_posts_custom_column' // Custom column content
```

## Hook Debugging

```php
// Check if action is being done
if ( doing_action( 'hook_name' ) ) { }

// Check if filter is being applied
if ( doing_filter( 'filter_name' ) ) { }

// Get current filter/action
$current = current_filter();

// Check if hook has callbacks
if ( has_action( 'hook_name' ) ) { }
if ( has_filter( 'filter_name' ) ) { }

// Count action executions
$count = did_action( 'hook_name' );

// List all hooks (debug)
global $wp_filter;
print_r( $wp_filter['hook_name'] );
```

## Best Practices

1. **Use appropriate priority**: Default is 10, use lower for earlier, higher for later
2. **Always return in filters**: Filters must return a value
3. **Document accepted args**: Specify number of arguments
4. **Use descriptive hook names**: Prefix custom hooks (e.g., `mytheme_after_header`)
5. **Check hook exists**: Use `has_action()` or `has_filter()` when needed
6. **Remove cleanly**: Match priority when removing callbacks
