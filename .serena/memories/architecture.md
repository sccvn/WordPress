# WordPress Architecture

## Directory Structure

```
WordPress/
├── index.php              # Main entry point
├── wp-load.php            # Bootstrap file
├── wp-blog-header.php     # Loads WordPress environment
├── wp-settings.php        # Core settings and includes
├── wp-config.php          # Configuration (user-created)
├── wp-config-sample.php   # Sample configuration
├── wp-login.php           # User authentication
├── wp-signup.php          # User registration
├── wp-cron.php            # Scheduled tasks
├── xmlrpc.php             # XML-RPC API
│
├── wp-admin/              # Admin interface
│   ├── index.php          # Admin dashboard
│   ├── admin.php          # Admin bootstrap
│   ├── admin-ajax.php     # AJAX handler
│   ├── includes/          # Admin helper classes
│   │   ├── class-wp-list-table.php   # List table base
│   │   ├── class-wp-upgrader.php     # Update system
│   │   ├── ajax-actions.php          # AJAX actions
│   │   └── ...
│   ├── css/               # Admin stylesheets
│   ├── js/                # Admin JavaScript
│   └── images/            # Admin images
│
├── wp-content/            # User content (plugins/themes)
│   ├── plugins/           # Installed plugins
│   ├── themes/            # Installed themes
│   ├── uploads/           # User uploads (created at runtime)
│   └── index.php          # Security file
│
└── wp-includes/           # Core WordPress library
    ├── version.php        # Version constants
    ├── functions.php      # Core functions
    ├── plugin.php         # Plugin/Hook API
    ├── post.php           # Post functions
    ├── taxonomy.php       # Taxonomy functions
    ├── user.php           # User functions
    ├── query.php          # Query functions
    ├── option.php         # Options API
    ├── formatting.php     # String/content formatting
    ├── capabilities.php   # User capabilities
    ├── kses.php           # Content sanitization
    ├── class-wp.php       # Main WordPress class
    ├── class-wp-query.php # Query class
    ├── class-wp-post.php  # Post class
    ├── class-wp-user.php  # User class
    ├── class-wp-hook.php  # Hook implementation
    ├── class-wpdb.php     # Database abstraction
    │
    ├── rest-api/          # REST API
    │   ├── class-wp-rest-server.php
    │   ├── class-wp-rest-request.php
    │   ├── class-wp-rest-response.php
    │   ├── endpoints/     # API endpoints
    │   ├── fields/        # Field handlers
    │   └── search/        # Search handlers
    │
    ├── blocks/            # Block types
    ├── widgets/           # Widget classes
    ├── customize/         # Customizer classes
    ├── sitemaps/          # XML sitemap
    ├── html-api/          # HTML parsing API
    ├── interactivity-api/ # Interactivity API
    └── ...
```

## Core Architecture Patterns

### 1. Hook System (Actions & Filters)

WordPress uses an event-driven architecture based on hooks:

```php
// Actions - Execute code at specific points
add_action('hook_name', 'callback_function', $priority, $accepted_args);
do_action('hook_name', $arg1, $arg2);

// Filters - Modify data
add_filter('filter_name', 'callback_function', $priority, $accepted_args);
$value = apply_filters('filter_name', $value, $arg1, $arg2);
```

### 2. Database Layer (WPDB)

Global `$wpdb` object for database operations:

```php
global $wpdb;
$wpdb->query($sql);
$wpdb->get_results($sql);
$wpdb->get_row($sql);
$wpdb->get_var($sql);
$wpdb->insert($table, $data);
$wpdb->update($table, $data, $where);
$wpdb->delete($table, $where);
$wpdb->prepare($sql, $args);
```

### 3. The Loop

Template pattern for displaying posts:

```php
if (have_posts()) {
    while (have_posts()) {
        the_post();
        // Display post content
    }
}
```

### 4. WP_Query

Object-oriented query system:

```php
$query = new WP_Query([
    'post_type' => 'post',
    'posts_per_page' => 10,
    'orderby' => 'date',
    'order' => 'DESC'
]);
```

## Bootstrap Sequence

1. `index.php` → loads `wp-blog-header.php`
2. `wp-blog-header.php` → loads `wp-load.php`
3. `wp-load.php` → finds and loads `wp-config.php`
4. `wp-config.php` → defines constants, loads `wp-settings.php`
5. `wp-settings.php` → loads core files, initializes WordPress

## Key Global Objects

- `$wp` - Main WordPress object
- `$wp_query` - Current query
- `$post` - Current post
- `$wpdb` - Database object
- `$wp_rewrite` - Rewrite rules
- `$wp_filter` - Registered hooks
- `$wp_actions` - Executed actions
