# WordPress Development Instructions

## Quick Reference

This document provides essential instructions for WordPress development within the HIVE multi-agent system.

## Development Setup

### Prerequisites
```bash
# PHP 7.4+ (8.0+ recommended)
php -v

# MySQL 5.7+ or MariaDB 10.4+
mysql --version

# Composer
composer --version

# Node.js 18+ and npm
node -v && npm -v

# WP-CLI
wp --version
```

### Local Environment
```bash
# Clone WordPress
git clone https://github.com/WordPress/WordPress.git
cd WordPress

# Install dependencies
composer install
npm install

# Set up database
wp config create --dbname=wordpress --dbuser=root --dbpass=root
wp db create
wp core install --url=localhost:8080 --title="Dev Site" --admin_user=admin --admin_password=admin --admin_email=admin@example.com
```

## Coding Standards

### PHP
```php
<?php
/**
 * Function description.
 *
 * @since 7.0.0
 *
 * @param string $param Parameter description.
 * @return bool Return description.
 */
function wp_function_name( $param ) {
    // Yoda conditions
    if ( true === $param ) {
        return true;
    }
    
    return false;
}
```

### JavaScript
```javascript
/**
 * Function description.
 *
 * @since 7.0.0
 *
 * @param {string} param - Parameter description.
 * @return {boolean} Return description.
 */
function wpFunctionName( param ) {
    if ( param === true ) {
        return true;
    }
    
    return false;
}
```

## Testing Commands

### PHPUnit
```bash
# Run all tests
./vendor/bin/phpunit

# Run specific test file
./vendor/bin/phpunit tests/phpunit/tests/rest-api/rest-posts-controller.php

# Run specific test method
./vendor/bin/phpunit --filter test_method_name

# Run with coverage
./vendor/bin/phpunit --coverage-html coverage/
```

### PHPCS
```bash
# Check code style
./vendor/bin/phpcs --standard=WordPress wp-includes/file.php

# Auto-fix issues
./vendor/bin/phpcbf --standard=WordPress wp-includes/file.php
```

### JavaScript
```bash
# Run JS tests
npm test

# Lint JavaScript
npm run lint:js

# Lint CSS
npm run lint:css
```

## WordPress Patterns

### Hooks (Actions/Filters)
```php
<?php
// Register action
add_action( 'init', 'my_init_function' );

// Register filter
add_filter( 'the_content', 'my_content_filter' );

// Fire action
do_action( 'my_custom_action', $param );

// Apply filter
$value = apply_filters( 'my_custom_filter', $value, $context );
```

### Options API
```php
<?php
// Get option
$value = get_option( 'my_option', 'default' );

// Update option
update_option( 'my_option', $value );

// Delete option
delete_option( 'my_option' );
```

### Database Operations
```php
<?php
global $wpdb;

// Prepared query
$results = $wpdb->get_results(
    $wpdb->prepare(
        "SELECT * FROM {$wpdb->posts} WHERE post_type = %s",
        'post'
    )
);

// Insert
$wpdb->insert(
    $wpdb->prefix . 'table',
    array( 'column' => 'value' ),
    array( '%s' )
);
```

### REST API
```php
<?php
// Register endpoint
add_action( 'rest_api_init', function() {
    register_rest_route( 'myplugin/v1', '/items', array(
        'methods'  => 'GET',
        'callback' => 'get_items_callback',
        'permission_callback' => '__return_true',
    ) );
} );

// Callback
function get_items_callback( WP_REST_Request $request ) {
    return new WP_REST_Response( array( 'items' => array() ), 200 );
}
```

## Security Checklist

### Input Validation
```php
<?php
$text = sanitize_text_field( $_POST['text'] );
$int = absint( $_GET['id'] );
$email = sanitize_email( $_POST['email'] );
$url = esc_url_raw( $_POST['url'] );
$html = wp_kses_post( $_POST['content'] );
```

### Output Escaping
```php
<?php
echo esc_html( $text );
echo esc_attr( $attribute );
echo esc_url( $url );
echo wp_kses_post( $html );
```

### Nonces
```php
<?php
// In form
wp_nonce_field( 'action_name', 'nonce_field' );

// Verify
if ( ! wp_verify_nonce( $_POST['nonce_field'], 'action_name' ) ) {
    die( 'Security check failed' );
}
```

### Capabilities
```php
<?php
if ( ! current_user_can( 'edit_posts' ) ) {
    wp_die( 'Permission denied' );
}
```

## File Structure

```
wp-includes/           Core includes
├── class-wp-*.php    Core classes
├── rest-api/         REST API
├── blocks/           Block editor
├── js/               JavaScript
└── css/              Stylesheets

wp-admin/             Admin area
├── includes/         Admin includes
├── js/               Admin JavaScript
└── css/              Admin styles

wp-content/           User content
├── themes/           Themes
├── plugins/          Plugins
└── uploads/          Media uploads

tests/                Test files
└── phpunit/          PHPUnit tests
```

## Agent Quick Reference

| Task | Agent |
|------|-------|
| Extract architecture | `@lead-Solution_Architect` |
| Plan feature | `@lead-Business_Analyst` |
| Write BDD scenarios | `@senior-Business_Analyst` |
| Implement PHP | `@senior-Backend_Developer` |
| Implement JS | `@senior-Frontend_Developer` |
| Write tests | `@senior-Automation_Tester` |
| Review code | `@lead-Code_Reviewer` |
| Security scan | `@senior-Security_Analyst` |
| Quality check | `@senior-Quality_Engineer` |

## Useful WP-CLI Commands

```bash
# Posts
wp post list
wp post create --post_title="Test" --post_status=publish

# Users
wp user list
wp user create testuser test@example.com

# Plugins
wp plugin list
wp plugin activate plugin-name

# Database
wp db export backup.sql
wp db import backup.sql

# Cron
wp cron event list
wp cron event run --all

# Cache
wp cache flush
wp transient delete --all
```
