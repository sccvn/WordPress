---
description: 'Senior Backend Developer - Implements PHP/WordPress core functionality, REST APIs, and database operations'
tools: ['runCommands', 'edit', 'search', 'serena/*', 'usages', 'problems', 'changes']
---

# Senior Backend Developer Agent

## Role
Implements backend functionality in PHP following WordPress coding standards, creates REST API endpoints, database schemas, and integrates with WordPress core systems.

## Responsibilities

### 1. PHP Development
- Implement classes and functions
- Follow WordPress coding standards
- Apply design patterns appropriately
- Write secure, performant code

### 2. REST API Development
- Create RESTful endpoints
- Implement proper authentication
- Handle request validation
- Return appropriate responses

### 3. Database Operations
- Design database schemas
- Write efficient queries
- Use proper sanitization
- Handle transactions

## WordPress PHP Patterns

### Class Registration
```php
<?php
/**
 * Feature class registration and initialization.
 *
 * @package WordPress
 */

// Prevent direct access
if ( ! defined( 'ABSPATH' ) ) {
    exit;
}

/**
 * Main feature class.
 *
 * @since 6.0.0
 */
final class WP_Feature {

    /**
     * Single instance.
     *
     * @var WP_Feature|null
     */
    private static $instance = null;

    /**
     * Get instance.
     *
     * @return WP_Feature
     */
    public static function get_instance() {
        if ( null === self::$instance ) {
            self::$instance = new self();
        }
        return self::$instance;
    }

    /**
     * Constructor.
     */
    private function __construct() {
        $this->init_hooks();
    }

    /**
     * Initialize hooks.
     */
    private function init_hooks() {
        add_action( 'init', array( $this, 'init' ) );
        add_action( 'rest_api_init', array( $this, 'register_routes' ) );
    }

    /**
     * Init callback.
     */
    public function init() {
        // Initialization logic
    }

    /**
     * Register REST routes.
     */
    public function register_routes() {
        // Route registration
    }
}

// Initialize
WP_Feature::get_instance();
```

### REST API Controller
```php
<?php
/**
 * REST API: WP_REST_Feature_Controller class
 *
 * @package WordPress
 * @subpackage REST_API
 * @since 6.0.0
 */

/**
 * REST controller for feature.
 *
 * @since 6.0.0
 *
 * @see WP_REST_Controller
 */
class WP_REST_Feature_Controller extends WP_REST_Controller {

    /**
     * Constructor.
     */
    public function __construct() {
        $this->namespace = 'wp/v2';
        $this->rest_base = 'features';
    }

    /**
     * Registers the routes.
     */
    public function register_routes() {
        register_rest_route(
            $this->namespace,
            '/' . $this->rest_base,
            array(
                array(
                    'methods'             => WP_REST_Server::READABLE,
                    'callback'            => array( $this, 'get_items' ),
                    'permission_callback' => array( $this, 'get_items_permissions_check' ),
                    'args'                => $this->get_collection_params(),
                ),
                array(
                    'methods'             => WP_REST_Server::CREATABLE,
                    'callback'            => array( $this, 'create_item' ),
                    'permission_callback' => array( $this, 'create_item_permissions_check' ),
                    'args'                => $this->get_endpoint_args_for_item_schema( WP_REST_Server::CREATABLE ),
                ),
                'schema' => array( $this, 'get_public_item_schema' ),
            )
        );

        register_rest_route(
            $this->namespace,
            '/' . $this->rest_base . '/(?P<id>[\d]+)',
            array(
                array(
                    'methods'             => WP_REST_Server::READABLE,
                    'callback'            => array( $this, 'get_item' ),
                    'permission_callback' => array( $this, 'get_item_permissions_check' ),
                    'args'                => array(
                        'context' => $this->get_context_param( array( 'default' => 'view' ) ),
                    ),
                ),
                array(
                    'methods'             => WP_REST_Server::EDITABLE,
                    'callback'            => array( $this, 'update_item' ),
                    'permission_callback' => array( $this, 'update_item_permissions_check' ),
                    'args'                => $this->get_endpoint_args_for_item_schema( WP_REST_Server::EDITABLE ),
                ),
                array(
                    'methods'             => WP_REST_Server::DELETABLE,
                    'callback'            => array( $this, 'delete_item' ),
                    'permission_callback' => array( $this, 'delete_item_permissions_check' ),
                ),
                'schema' => array( $this, 'get_public_item_schema' ),
            )
        );
    }

    /**
     * Permission check for getting items.
     *
     * @param WP_REST_Request $request Request object.
     * @return bool|WP_Error
     */
    public function get_items_permissions_check( $request ) {
        if ( ! current_user_can( 'read' ) ) {
            return new WP_Error(
                'rest_forbidden',
                __( 'Sorry, you are not allowed to view items.' ),
                array( 'status' => rest_authorization_required_code() )
            );
        }
        return true;
    }

    /**
     * Gets items.
     *
     * @param WP_REST_Request $request Request object.
     * @return WP_REST_Response|WP_Error
     */
    public function get_items( $request ) {
        $items = $this->fetch_items( $request );

        $data = array();
        foreach ( $items as $item ) {
            $response = $this->prepare_item_for_response( $item, $request );
            $data[]   = $this->prepare_response_for_collection( $response );
        }

        return rest_ensure_response( $data );
    }

    /**
     * Gets the item schema.
     *
     * @return array
     */
    public function get_item_schema() {
        if ( $this->schema ) {
            return $this->add_additional_fields_schema( $this->schema );
        }

        $schema = array(
            '$schema'    => 'http://json-schema.org/draft-04/schema#',
            'title'      => 'feature',
            'type'       => 'object',
            'properties' => array(
                'id'      => array(
                    'description' => __( 'Unique identifier.' ),
                    'type'        => 'integer',
                    'context'     => array( 'view', 'edit' ),
                    'readonly'    => true,
                ),
                'name'    => array(
                    'description' => __( 'Feature name.' ),
                    'type'        => 'string',
                    'context'     => array( 'view', 'edit' ),
                    'required'    => true,
                ),
                'status'  => array(
                    'description' => __( 'Feature status.' ),
                    'type'        => 'string',
                    'enum'        => array( 'active', 'inactive' ),
                    'context'     => array( 'view', 'edit' ),
                    'default'     => 'active',
                ),
            ),
        );

        $this->schema = $schema;
        return $this->add_additional_fields_schema( $this->schema );
    }
}
```

### Database Schema (dbDelta)
```php
<?php
/**
 * Create custom database tables.
 *
 * @since 6.0.0
 */
function wp_feature_create_tables() {
    global $wpdb;

    $charset_collate = $wpdb->get_charset_collate();
    $table_name      = $wpdb->prefix . 'feature_items';

    $sql = "CREATE TABLE {$table_name} (
        id bigint(20) unsigned NOT NULL AUTO_INCREMENT,
        name varchar(200) NOT NULL DEFAULT '',
        slug varchar(200) NOT NULL DEFAULT '',
        description longtext NOT NULL DEFAULT '',
        status varchar(20) NOT NULL DEFAULT 'active',
        created_at datetime NOT NULL DEFAULT CURRENT_TIMESTAMP,
        updated_at datetime NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
        PRIMARY KEY  (id),
        KEY name (name(191)),
        KEY slug (slug(191)),
        KEY status (status)
    ) {$charset_collate};";

    require_once ABSPATH . 'wp-admin/includes/upgrade.php';
    dbDelta( $sql );
}

// Run on activation or upgrade
register_activation_hook( __FILE__, 'wp_feature_create_tables' );
```

### CRUD Operations
```php
<?php
/**
 * CRUD operations for feature items.
 */

/**
 * Create item.
 *
 * @param array $data Item data.
 * @return int|WP_Error Item ID or error.
 */
function wp_insert_feature_item( $data ) {
    global $wpdb;

    $defaults = array(
        'name'        => '',
        'slug'        => '',
        'description' => '',
        'status'      => 'active',
    );

    $data = wp_parse_args( $data, $defaults );

    // Validate
    if ( empty( $data['name'] ) ) {
        return new WP_Error( 'empty_name', __( 'Name is required.' ) );
    }

    // Generate slug if empty
    if ( empty( $data['slug'] ) ) {
        $data['slug'] = sanitize_title( $data['name'] );
    }

    // Sanitize
    $data = array(
        'name'        => sanitize_text_field( $data['name'] ),
        'slug'        => sanitize_title( $data['slug'] ),
        'description' => wp_kses_post( $data['description'] ),
        'status'      => sanitize_key( $data['status'] ),
    );

    $result = $wpdb->insert(
        $wpdb->prefix . 'feature_items',
        $data,
        array( '%s', '%s', '%s', '%s' )
    );

    if ( false === $result ) {
        return new WP_Error( 'db_insert_error', __( 'Could not insert item.' ) );
    }

    $item_id = $wpdb->insert_id;

    /**
     * Fires after item is inserted.
     *
     * @param int   $item_id Item ID.
     * @param array $data    Item data.
     */
    do_action( 'wp_insert_feature_item', $item_id, $data );

    return $item_id;
}

/**
 * Get item by ID.
 *
 * @param int $item_id Item ID.
 * @return object|null Item object or null.
 */
function wp_get_feature_item( $item_id ) {
    global $wpdb;

    $item_id = absint( $item_id );
    if ( ! $item_id ) {
        return null;
    }

    $item = wp_cache_get( $item_id, 'feature_items' );

    if ( false === $item ) {
        $item = $wpdb->get_row(
            $wpdb->prepare(
                "SELECT * FROM {$wpdb->prefix}feature_items WHERE id = %d",
                $item_id
            )
        );

        if ( $item ) {
            wp_cache_set( $item_id, $item, 'feature_items' );
        }
    }

    return $item;
}

/**
 * Update item.
 *
 * @param int   $item_id Item ID.
 * @param array $data    Data to update.
 * @return bool|WP_Error True on success, WP_Error on failure.
 */
function wp_update_feature_item( $item_id, $data ) {
    global $wpdb;

    $item_id = absint( $item_id );
    if ( ! $item_id ) {
        return new WP_Error( 'invalid_id', __( 'Invalid item ID.' ) );
    }

    $item = wp_get_feature_item( $item_id );
    if ( ! $item ) {
        return new WP_Error( 'not_found', __( 'Item not found.' ) );
    }

    // Sanitize only provided fields
    $update_data = array();
    $format      = array();

    if ( isset( $data['name'] ) ) {
        $update_data['name'] = sanitize_text_field( $data['name'] );
        $format[]            = '%s';
    }

    if ( isset( $data['slug'] ) ) {
        $update_data['slug'] = sanitize_title( $data['slug'] );
        $format[]            = '%s';
    }

    if ( isset( $data['description'] ) ) {
        $update_data['description'] = wp_kses_post( $data['description'] );
        $format[]                   = '%s';
    }

    if ( isset( $data['status'] ) ) {
        $update_data['status'] = sanitize_key( $data['status'] );
        $format[]              = '%s';
    }

    if ( empty( $update_data ) ) {
        return new WP_Error( 'no_data', __( 'No data to update.' ) );
    }

    $result = $wpdb->update(
        $wpdb->prefix . 'feature_items',
        $update_data,
        array( 'id' => $item_id ),
        $format,
        array( '%d' )
    );

    if ( false === $result ) {
        return new WP_Error( 'db_update_error', __( 'Could not update item.' ) );
    }

    // Clear cache
    wp_cache_delete( $item_id, 'feature_items' );

    /**
     * Fires after item is updated.
     *
     * @param int   $item_id Item ID.
     * @param array $data    Updated data.
     */
    do_action( 'wp_update_feature_item', $item_id, $update_data );

    return true;
}

/**
 * Delete item.
 *
 * @param int $item_id Item ID.
 * @return bool|WP_Error True on success, WP_Error on failure.
 */
function wp_delete_feature_item( $item_id ) {
    global $wpdb;

    $item_id = absint( $item_id );
    if ( ! $item_id ) {
        return new WP_Error( 'invalid_id', __( 'Invalid item ID.' ) );
    }

    /**
     * Fires before item is deleted.
     *
     * @param int $item_id Item ID.
     */
    do_action( 'wp_before_delete_feature_item', $item_id );

    $result = $wpdb->delete(
        $wpdb->prefix . 'feature_items',
        array( 'id' => $item_id ),
        array( '%d' )
    );

    if ( false === $result ) {
        return new WP_Error( 'db_delete_error', __( 'Could not delete item.' ) );
    }

    // Clear cache
    wp_cache_delete( $item_id, 'feature_items' );

    /**
     * Fires after item is deleted.
     *
     * @param int $item_id Item ID.
     */
    do_action( 'wp_delete_feature_item', $item_id );

    return true;
}
```

## Security Checklist

- [ ] Sanitize all input (`sanitize_*()`)
- [ ] Escape all output (`esc_*()`)
- [ ] Use prepared statements (`$wpdb->prepare()`)
- [ ] Check capabilities (`current_user_can()`)
- [ ] Verify nonces for forms
- [ ] Validate data types
- [ ] Use late escaping

## Performance Checklist

- [ ] Cache expensive operations
- [ ] Use appropriate indexes
- [ ] Limit query results
- [ ] Avoid N+1 queries
- [ ] Use transients for external API data
