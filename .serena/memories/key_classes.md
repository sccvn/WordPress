# Key WordPress Classes Reference

## Core Classes

### WP_Query
The main query class for retrieving posts from the database.

**Location**: `wp-includes/class-wp-query.php`

```php
$query = new WP_Query( array(
    'post_type'      => 'post',
    'posts_per_page' => 10,
    'post_status'    => 'publish',
    'orderby'        => 'date',
    'order'          => 'DESC',
    'meta_query'     => array(
        array(
            'key'     => 'featured',
            'value'   => '1',
            'compare' => '='
        )
    ),
    'tax_query'      => array(
        array(
            'taxonomy' => 'category',
            'field'    => 'slug',
            'terms'    => 'news'
        )
    )
) );

// The Loop
if ( $query->have_posts() ) {
    while ( $query->have_posts() ) {
        $query->the_post();
        // Display post
    }
    wp_reset_postdata();
}
```

### WP_Post
Represents a post object.

**Location**: `wp-includes/class-wp-post.php`

```php
// Get post
$post = get_post( $post_id );
$post = WP_Post::get_instance( $post_id );

// Properties
$post->ID;
$post->post_title;
$post->post_content;
$post->post_excerpt;
$post->post_status;
$post->post_type;
$post->post_author;
$post->post_date;
$post->post_modified;
$post->guid;
```

### WP_User
Represents a user object.

**Location**: `wp-includes/class-wp-user.php`

```php
$user = new WP_User( $user_id );
$user = get_user_by( 'email', 'user@example.com' );
$user = wp_get_current_user();

// Properties
$user->ID;
$user->user_login;
$user->user_email;
$user->display_name;
$user->roles;
$user->caps;

// Methods
$user->has_cap( 'edit_posts' );
$user->add_role( 'editor' );
$user->remove_role( 'subscriber' );
```

### WP_Term
Represents a taxonomy term.

**Location**: `wp-includes/class-wp-term.php`

```php
$term = get_term( $term_id, 'category' );
$term = get_term_by( 'slug', 'news', 'category' );

// Properties
$term->term_id;
$term->name;
$term->slug;
$term->taxonomy;
$term->description;
$term->parent;
$term->count;
```

### wpdb (Database)
Database abstraction class.

**Location**: `wp-includes/class-wpdb.php`

```php
global $wpdb;

// Select
$results = $wpdb->get_results( "SELECT * FROM {$wpdb->posts}" );
$row = $wpdb->get_row( "SELECT * FROM {$wpdb->posts} WHERE ID = 1" );
$var = $wpdb->get_var( "SELECT COUNT(*) FROM {$wpdb->posts}" );
$col = $wpdb->get_col( "SELECT ID FROM {$wpdb->posts}" );

// Insert
$wpdb->insert(
    $wpdb->posts,
    array( 'post_title' => 'Title', 'post_content' => 'Content' ),
    array( '%s', '%s' )
);
$insert_id = $wpdb->insert_id;

// Update
$wpdb->update(
    $wpdb->posts,
    array( 'post_title' => 'New Title' ),
    array( 'ID' => 1 ),
    array( '%s' ),
    array( '%d' )
);

// Delete
$wpdb->delete( $wpdb->posts, array( 'ID' => 1 ), array( '%d' ) );

// Prepared statements (ALWAYS use for user input)
$wpdb->prepare(
    "SELECT * FROM {$wpdb->posts} WHERE post_status = %s AND post_author = %d",
    'publish',
    $user_id
);

// Table names
$wpdb->posts;
$wpdb->postmeta;
$wpdb->users;
$wpdb->usermeta;
$wpdb->options;
$wpdb->terms;
$wpdb->term_taxonomy;
$wpdb->term_relationships;
$wpdb->comments;
$wpdb->commentmeta;
```

### WP_Hook
Implements the hook system.

**Location**: `wp-includes/class-wp-hook.php`

```php
// Used internally by add_action, add_filter, etc.
// Stores callbacks, priorities, and manages execution
```

### WP_Error
Error handling class.

**Location**: `wp-includes/class-wp-error.php`

```php
// Create error
$error = new WP_Error( 'error_code', 'Error message', $data );

// Check if error
if ( is_wp_error( $result ) ) {
    $code = $result->get_error_code();
    $message = $result->get_error_message();
    $data = $result->get_error_data();
    $messages = $result->get_error_messages();
}

// Add error
$error->add( 'another_code', 'Another message' );
```

## REST API Classes

### WP_REST_Server
**Location**: `wp-includes/rest-api/class-wp-rest-server.php`

### WP_REST_Request
**Location**: `wp-includes/rest-api/class-wp-rest-request.php`

```php
// In endpoint callback
function my_endpoint( WP_REST_Request $request ) {
    $param = $request->get_param( 'param_name' );
    $params = $request->get_params();
    $json = $request->get_json_params();
    $body = $request->get_body();
    $headers = $request->get_headers();
}
```

### WP_REST_Response
**Location**: `wp-includes/rest-api/class-wp-rest-response.php`

```php
$response = new WP_REST_Response( $data, 200 );
$response->set_headers( array( 'X-Custom' => 'value' ) );
return $response;
```

## Admin Classes

### WP_List_Table
Base class for admin list tables.

**Location**: `wp-admin/includes/class-wp-list-table.php`

### WP_Screen
Admin screen helper.

**Location**: `wp-admin/includes/class-wp-screen.php`

## Customizer Classes

### WP_Customize_Manager
**Location**: `wp-includes/class-wp-customize-manager.php`

### WP_Customize_Setting
**Location**: `wp-includes/class-wp-customize-setting.php`

### WP_Customize_Control
**Location**: `wp-includes/class-wp-customize-control.php`

## Block Editor Classes

### WP_Block
**Location**: `wp-includes/class-wp-block.php`

### WP_Block_Type
**Location**: `wp-includes/class-wp-block-type.php`

### WP_Block_Type_Registry
**Location**: `wp-includes/class-wp-block-type-registry.php`
