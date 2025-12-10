# WordPress Low-Level Architecture Documentation

## Overview

This document provides a comprehensive low-level architecture analysis of the WordPress core system, extracted using the HIVE multi-agent system.

**Generated:** 2024
**WordPress Version:** 7.0-alpha
**Analysis Scope:** Core architecture, design patterns, and key subsystems

## Table of Contents

1. [System Context](#1-system-context)
2. [Container Architecture](#2-container-architecture)
3. [Core Components](#3-core-components)
4. [Bootstrap Sequence](#4-bootstrap-sequence)
5. [Hook System Architecture](#5-hook-system-architecture)
6. [Database Layer](#6-database-layer)
7. [REST API Architecture](#7-rest-api-architecture)
8. [Block Editor Architecture](#8-block-editor-architecture)
9. [Design Patterns](#9-design-patterns)
10. [Class Diagrams](#10-class-diagrams)
11. [Sequence Diagrams](#11-sequence-diagrams)

---

## 1. System Context

### C4 Level 1 - System Context Diagram

```
See: diagrams/c4-system-context.puml
```

WordPress operates as a content management system that interacts with:
- **Users**: Content creators, administrators, visitors
- **Database**: MySQL/MariaDB for persistent storage
- **External Services**: Theme/plugin repositories, update servers, CDNs
- **Mail Server**: Email notifications

---

## 2. Container Architecture

### C4 Level 2 - Container Diagram

```
See: diagrams/c4-container.puml
```

WordPress consists of these primary containers:

| Container | Technology | Purpose |
|-----------|------------|---------|
| Frontend | PHP/HTML/CSS/JS | User-facing content delivery |
| Admin Panel | PHP/React | Content management interface |
| REST API | PHP/JSON | Programmatic access |
| Database | MySQL | Data persistence |
| Cron System | PHP | Scheduled tasks |

---

## 3. Core Components

### 3.1 Main WordPress Class (`WP`)

**Location:** `wp-includes/class-wp.php`

The `WP` class is the main orchestrator for the frontend request lifecycle.

**Key Properties:**
- `$public_query_vars` - Public query variables
- `$private_query_vars` - Private query variables  
- `$query_vars` - Current query variables
- `$query_string` - Built query string
- `$request` - Current request path
- `$matched_rule` - Matched rewrite rule
- `$matched_query` - Matched query

**Key Methods:**
| Method | Purpose |
|--------|---------|
| `init()` | Initialize WordPress environment |
| `parse_request()` | Parse incoming request and set query vars |
| `send_headers()` | Send HTTP headers |
| `query_posts()` | Set up main query |
| `handle_404()` | Handle 404 errors |
| `main()` | Main execution method (orchestrator) |

### 3.2 Query System (`WP_Query`)

**Location:** `wp-includes/class-wp-query.php`

The `WP_Query` class handles all content querying operations.

**Key Properties:**
- `$query` - Original query string
- `$query_vars` - Parsed query variables
- `$tax_query` - Taxonomy query object
- `$meta_query` - Meta query object
- `$date_query` - Date query object
- `$posts` - Query results
- `$post_count` - Number of posts in current page
- `$found_posts` - Total posts found
- `$max_num_pages` - Total pages
- `$is_*` - Boolean query type flags

**Key Methods:**
| Method | Purpose |
|--------|---------|
| `__construct()` | Initialize and optionally run query |
| `init()` | Reset query state |
| `parse_query()` | Parse query arguments |
| `get_posts()` | Execute query and return posts |
| `have_posts()` | Check if posts remain in loop |
| `the_post()` | Set up current post |
| `rewind_posts()` | Reset post iteration |
| `setup_postdata()` | Set up global post data |

### 3.3 Database Layer (`wpdb`)

**Location:** `wp-includes/class-wpdb.php`

The `wpdb` class provides database abstraction.

**Key Properties:**
- `$show_errors` - Display errors flag
- `$num_queries` - Query count
- `$last_query` - Last executed query
- `$last_result` - Last query result
- `$insert_id` - Last insert ID
- `$prefix` - Table prefix
- `$tables` - Core table names

**Key Methods:**
| Method | Purpose |
|--------|---------|
| `query()` | Execute raw SQL |
| `prepare()` | Prepare SQL with placeholders |
| `get_var()` | Get single value |
| `get_row()` | Get single row |
| `get_col()` | Get single column |
| `get_results()` | Get multiple rows |
| `insert()` | Insert row |
| `update()` | Update rows |
| `delete()` | Delete rows |
| `replace()` | Replace row |

### 3.4 Hook System (`WP_Hook`)

**Location:** `wp-includes/class-wp-hook.php`

The `WP_Hook` class implements the Observer pattern for extensibility.

**Key Properties:**
- `$callbacks` - Registered callbacks by priority
- `$priorities` - Sorted priority list
- `$iterations` - Current iteration stack
- `$nesting_level` - Recursive call depth
- `$doing_action` - Action execution flag

**Key Methods:**
| Method | Purpose |
|--------|---------|
| `add_filter()` | Register callback |
| `remove_filter()` | Unregister callback |
| `has_filter()` | Check if filter exists |
| `apply_filters()` | Execute filter chain |
| `do_action()` | Execute action chain |
| `current_priority()` | Get current execution priority |

---

## 4. Bootstrap Sequence

### Request Flow

```
1. index.php
   └── wp-blog-header.php
       ├── wp-load.php
       │   └── wp-config.php
       │       └── wp-settings.php (MAIN BOOTSTRAP)
       │           ├── version.php
       │           ├── load.php
       │           ├── default-constants.php
       │           ├── plugin.php (Hook system)
       │           ├── class-wpdb.php (Database)
       │           ├── default-filters.php
       │           ├── [Core classes loaded]
       │           ├── [Plugins loaded]
       │           ├── [Theme loaded]
       │           └── [init action fired]
       └── wp() - Run main query
           └── template-loader.php
```

### Key Bootstrap Actions

| Action | Timing | Purpose |
|--------|--------|---------|
| `muplugins_loaded` | After MU plugins | MU plugin initialization |
| `plugins_loaded` | After plugins | Plugin initialization |
| `setup_theme` | Before theme | Theme preparation |
| `after_setup_theme` | After theme | Theme initialization |
| `init` | Core ready | General initialization |
| `wp_loaded` | After WP loaded | Post-load actions |
| `wp` | After query | Query complete |
| `template_redirect` | Before template | Template selection |

---

## 5. Hook System Architecture

### Observer Pattern Implementation

WordPress hooks implement the Observer pattern with two variants:

**Actions:** Execute callbacks at specific points
```php
do_action('hook_name', $arg1, $arg2);
add_action('hook_name', 'callback', $priority, $accepted_args);
```

**Filters:** Transform data through callback chain
```php
$value = apply_filters('filter_name', $value, $context);
add_filter('filter_name', 'callback', $priority, $accepted_args);
```

### Hook Execution Flow

```
add_filter()
    │
    ▼
WP_Hook::add_filter()
    │ Store callback in $callbacks[priority]
    ▼
apply_filters()
    │
    ▼
WP_Hook::apply_filters()
    │ Sort by priority
    │ Iterate callbacks
    │ Pass value through chain
    ▼
Return modified value
```

### Priority System

- Default priority: 10
- Lower number = earlier execution
- Same priority = registration order
- Priority range: 1-999 (typical)

---

## 6. Database Layer

### WPDB Architecture

```
Application Code
       │
       ▼
   $wpdb->method()
       │
       ├── prepare() - Escape/format SQL
       │
       ▼
   $wpdb->query()
       │
       ├── check_connection() - Verify DB connection
       │
       ├── _do_query() - Execute via mysqli
       │
       ├── log_query() - Debug logging
       │
       ▼
   Process Results
       │
       ├── get_results() - Multiple rows
       ├── get_row() - Single row
       ├── get_var() - Single value
       └── get_col() - Single column
```

### Core Tables

| Table | Purpose |
|-------|---------|
| `wp_posts` | All content (posts, pages, CPT) |
| `wp_postmeta` | Post metadata |
| `wp_users` | User accounts |
| `wp_usermeta` | User metadata |
| `wp_terms` | Taxonomy terms |
| `wp_term_taxonomy` | Term-taxonomy relationships |
| `wp_term_relationships` | Object-term relationships |
| `wp_options` | Site options |
| `wp_comments` | Comments |
| `wp_commentmeta` | Comment metadata |

### SQL Injection Prevention

```php
// ALWAYS use prepare() for user input
$results = $wpdb->get_results(
    $wpdb->prepare(
        "SELECT * FROM {$wpdb->posts} WHERE post_author = %d AND post_status = %s",
        $author_id,
        'publish'
    )
);
```

---

## 7. REST API Architecture

### Component Overview

```
HTTP Request
     │
     ▼
WP_REST_Server
     │
     ├── serve_request() - Entry point
     │
     ├── check_authentication()
     │
     ├── match_request_to_handler()
     │   └── Match route pattern to controller
     │
     ▼
WP_REST_Request
     │
     ├── Parse headers, body, params
     │
     ├── Validate against schema
     │
     ▼
WP_REST_Controller (endpoint specific)
     │
     ├── permission_callback()
     │
     ├── get_items() / create_item() / etc.
     │
     ▼
WP_REST_Response
     │
     ├── Set status, headers, body
     │
     └── JSON encode and return
```

### Key Classes

| Class | Purpose |
|-------|---------|
| `WP_REST_Server` | Route registration, request dispatch |
| `WP_REST_Request` | Request parsing, validation |
| `WP_REST_Response` | Response building |
| `WP_REST_Controller` | Base controller class |

### Endpoint Controllers

| Controller | Endpoints |
|------------|-----------|
| `WP_REST_Posts_Controller` | `/wp/v2/posts`, `/wp/v2/pages` |
| `WP_REST_Users_Controller` | `/wp/v2/users` |
| `WP_REST_Comments_Controller` | `/wp/v2/comments` |
| `WP_REST_Terms_Controller` | `/wp/v2/categories`, `/wp/v2/tags` |
| `WP_REST_Taxonomies_Controller` | `/wp/v2/taxonomies` |
| `WP_REST_Blocks_Controller` | `/wp/v2/blocks` |

### HTTP Method Constants

```php
WP_REST_Server::READABLE   = 'GET'
WP_REST_Server::CREATABLE  = 'POST'
WP_REST_Server::EDITABLE   = 'POST, PUT, PATCH'
WP_REST_Server::DELETABLE  = 'DELETE'
WP_REST_Server::ALLMETHODS = 'GET, POST, PUT, PATCH, DELETE'
```

---

## 8. Block Editor Architecture

### Block System Components

| Component | Location | Purpose |
|-----------|----------|---------|
| `WP_Block` | `class-wp-block.php` | Block instance |
| `WP_Block_Type` | `class-wp-block-type.php` | Block definition |
| `WP_Block_Type_Registry` | `class-wp-block-type-registry.php` | Block registration |
| `WP_Block_Parser` | `class-wp-block-parser.php` | Parse block content |
| `WP_Block_Patterns_Registry` | `class-wp-block-patterns-registry.php` | Pattern registration |

### Block Registration

```php
register_block_type('namespace/block-name', [
    'render_callback' => 'render_function',
    'attributes' => [...],
    'supports' => [...],
]);
```

### Block Parsing Flow

```
Post Content (HTML + comments)
         │
         ▼
  WP_Block_Parser
         │
         ├── parse() - Tokenize content
         │
         ▼
  WP_Block_Parser_Block[]
         │
         ├── Block name, attributes, inner content
         │
         ▼
  WP_Block_List
         │
         ├── Iterate blocks
         │
         ▼
  WP_Block::render()
         │
         ├── Apply render_callback
         │
         ▼
  Final HTML Output
```

---

## 9. Design Patterns

### Patterns Identified

| Pattern | Implementation | Location |
|---------|----------------|----------|
| **Observer** | Hook system (actions/filters) | `WP_Hook` |
| **Singleton** | Global instances ($wpdb, $wp_query) | Various |
| **Factory** | Post type/taxonomy registration | `register_post_type()` |
| **Registry** | Block type registry | `WP_Block_Type_Registry` |
| **Template Method** | Walker classes | `WP_Walker` |
| **Strategy** | Transports (HTTP, cURL) | `WP_HTTP` |
| **Iterator** | The Loop, block lists | `WP_Query`, `WP_Block_List` |
| **Facade** | High-level APIs | `get_posts()`, `wp_insert_post()` |
| **Adapter** | Database drivers | `wpdb` |

### Observer Pattern Detail

```php
// WP_Hook implements Observer pattern
class WP_Hook implements Iterator, ArrayAccess {
    public $callbacks = [];  // Observers by priority
    
    public function add_filter($callback, $priority) {
        // Subscribe observer
        $this->callbacks[$priority][$id] = $callback;
    }
    
    public function apply_filters($value, $args) {
        // Notify all observers
        foreach ($this->callbacks as $priority => $callbacks) {
            foreach ($callbacks as $callback) {
                $value = call_user_func_array($callback, $args);
            }
        }
        return $value;
    }
}
```

### Registry Pattern Detail

```php
// Singleton registry for block types
final class WP_Block_Type_Registry {
    private static $instance;
    private $registered_block_types = [];
    
    public static function get_instance() {
        if (null === self::$instance) {
            self::$instance = new self();
        }
        return self::$instance;
    }
    
    public function register($name, $args) {
        $this->registered_block_types[$name] = new WP_Block_Type($name, $args);
    }
}
```

---

## 10. Class Diagrams

```
See: diagrams/class-diagram-*.puml
```

### Core Class Relationships

```
WP (Main)
 ├── uses → WP_Rewrite
 ├── uses → WP_Query
 └── uses → wpdb

WP_Query
 ├── uses → WP_Tax_Query
 ├── uses → WP_Meta_Query
 ├── uses → WP_Date_Query
 └── uses → wpdb

WP_REST_Server
 ├── uses → WP_REST_Request
 ├── uses → WP_REST_Response
 └── dispatches → WP_REST_Controller (subclasses)

WP_Hook
 ├── implements → Iterator
 └── implements → ArrayAccess
```

---

## 11. Sequence Diagrams

```
See: diagrams/sequence-diagram-*.puml
```

### Request Lifecycle

1. HTTP Request arrives
2. `index.php` loads `wp-blog-header.php`
3. `wp-load.php` initializes environment
4. `wp-settings.php` bootstraps core
5. Plugins and theme load
6. `wp()` function executes main query
7. Template selected and rendered
8. Response sent to client

---

## Appendix

### File References

| File | Purpose |
|------|---------|
| `wp-settings.php` | Main bootstrap |
| `wp-includes/class-wp.php` | Main WP class |
| `wp-includes/class-wp-query.php` | Query system |
| `wp-includes/class-wpdb.php` | Database abstraction |
| `wp-includes/class-wp-hook.php` | Hook system |
| `wp-includes/plugin.php` | Hook API functions |
| `wp-includes/rest-api/` | REST API classes |
| `wp-includes/blocks/` | Block type definitions |

### Global Objects

| Object | Type | Purpose |
|--------|------|---------|
| `$wp` | `WP` | Main WordPress instance |
| `$wp_query` | `WP_Query` | Main query |
| `$wpdb` | `wpdb` | Database connection |
| `$wp_filter` | `WP_Hook[]` | Registered hooks |
| `$wp_actions` | `array` | Executed actions count |
| `$post` | `WP_Post` | Current post |
| `$wp_rewrite` | `WP_Rewrite` | Rewrite rules |
