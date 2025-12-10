# WordPress REST API Reference

## Overview

The WordPress REST API provides HTTP endpoints for accessing and manipulating WordPress data. It's the foundation for the block editor and enables headless WordPress.

## Base URL

```
/wp-json/wp/v2/
```

## Core Endpoints

### Posts
```
GET    /wp/v2/posts           # List posts
GET    /wp/v2/posts/<id>      # Get single post
POST   /wp/v2/posts           # Create post
PUT    /wp/v2/posts/<id>      # Update post
DELETE /wp/v2/posts/<id>      # Delete post
```

### Pages
```
GET    /wp/v2/pages           # List pages
GET    /wp/v2/pages/<id>      # Get single page
POST   /wp/v2/pages           # Create page
PUT    /wp/v2/pages/<id>      # Update page
DELETE /wp/v2/pages/<id>      # Delete page
```

### Users
```
GET    /wp/v2/users           # List users
GET    /wp/v2/users/<id>      # Get single user
GET    /wp/v2/users/me        # Get current user
POST   /wp/v2/users           # Create user
PUT    /wp/v2/users/<id>      # Update user
DELETE /wp/v2/users/<id>      # Delete user
```

### Categories
```
GET    /wp/v2/categories      # List categories
GET    /wp/v2/categories/<id> # Get single category
POST   /wp/v2/categories      # Create category
PUT    /wp/v2/categories/<id> # Update category
DELETE /wp/v2/categories/<id> # Delete category
```

### Tags
```
GET    /wp/v2/tags            # List tags
GET    /wp/v2/tags/<id>       # Get single tag
POST   /wp/v2/tags           # Create tag
PUT    /wp/v2/tags/<id>      # Update tag
DELETE /wp/v2/tags/<id>      # Delete tag
```

### Media
```
GET    /wp/v2/media           # List media
GET    /wp/v2/media/<id>      # Get single media
POST   /wp/v2/media           # Upload media
PUT    /wp/v2/media/<id>      # Update media
DELETE /wp/v2/media/<id>      # Delete media
```

### Comments
```
GET    /wp/v2/comments        # List comments
GET    /wp/v2/comments/<id>   # Get single comment
POST   /wp/v2/comments        # Create comment
PUT    /wp/v2/comments/<id>   # Update comment
DELETE /wp/v2/comments/<id>   # Delete comment
```

## Registering Custom Endpoints

```php
add_action( 'rest_api_init', function() {
    register_rest_route( 'myplugin/v1', '/items', array(
        'methods'             => 'GET',
        'callback'            => 'my_get_items',
        'permission_callback' => '__return_true',
    ) );

    register_rest_route( 'myplugin/v1', '/items/(?P<id>\d+)', array(
        array(
            'methods'             => 'GET',
            'callback'            => 'my_get_item',
            'permission_callback' => '__return_true',
            'args'                => array(
                'id' => array(
                    'validate_callback' => function( $param ) {
                        return is_numeric( $param );
                    }
                ),
            ),
        ),
        array(
            'methods'             => 'POST',
            'callback'            => 'my_update_item',
            'permission_callback' => function() {
                return current_user_can( 'edit_posts' );
            },
        ),
    ) );
} );

function my_get_items( WP_REST_Request $request ) {
    $items = get_my_items();
    return new WP_REST_Response( $items, 200 );
}

function my_get_item( WP_REST_Request $request ) {
    $id = $request->get_param( 'id' );
    $item = get_my_item( $id );

    if ( ! $item ) {
        return new WP_Error(
            'not_found',
            'Item not found',
            array( 'status' => 404 )
        );
    }

    return new WP_REST_Response( $item, 200 );
}
```

## Authentication

### Cookie Authentication (default for logged-in users)
```javascript
// JavaScript (in admin or with nonce)
fetch( '/wp-json/wp/v2/posts', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json',
        'X-WP-Nonce': wpApiSettings.nonce
    },
    body: JSON.stringify( { title: 'Test', status: 'publish' } )
} );
```

### Application Passwords (WordPress 5.6+)
```bash
curl -X POST \
  https://example.com/wp-json/wp/v2/posts \
  -u "username:application-password" \
  -H "Content-Type: application/json" \
  -d '{"title":"Test","status":"publish"}'
```

## Common Query Parameters

```
# Pagination
per_page=10
page=1
offset=0

# Ordering
orderby=date|title|id|include|slug|modified
order=asc|desc

# Filtering
status=publish|draft|pending|private|future
author=<user_id>
author_exclude=<user_id>
categories=1,2,3
categories_exclude=4,5
tags=1,2,3
tags_exclude=4,5
search=<term>
before=<date>
after=<date>
slug=<slug>
include=1,2,3
exclude=4,5,6

# Fields
_fields=id,title,content
_embed                    # Include embedded resources
```

## Extending Existing Endpoints

### Add Custom Fields to Responses
```php
add_action( 'rest_api_init', function() {
    register_rest_field( 'post', 'custom_field', array(
        'get_callback' => function( $post ) {
            return get_post_meta( $post['id'], 'custom_field', true );
        },
        'update_callback' => function( $value, $post ) {
            return update_post_meta( $post->ID, 'custom_field', $value );
        },
        'schema' => array(
            'type'        => 'string',
            'description' => 'Custom field description',
            'context'     => array( 'view', 'edit' ),
        ),
    ) );
} );
```

## Files Structure

```
wp-includes/rest-api/
├── class-wp-rest-server.php      # Main server class
├── class-wp-rest-request.php     # Request handling
├── class-wp-rest-response.php    # Response handling
├── endpoints/                    # Core endpoints
│   ├── class-wp-rest-posts-controller.php
│   ├── class-wp-rest-users-controller.php
│   ├── class-wp-rest-comments-controller.php
│   └── ...
├── fields/                       # Field handlers
└── search/                       # Search handlers
```
