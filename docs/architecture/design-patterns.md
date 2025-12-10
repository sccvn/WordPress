# WordPress Design Patterns

This document catalogs the design patterns identified in the WordPress core codebase.

## Table of Contents

1. [Observer Pattern](#1-observer-pattern)
2. [Singleton Pattern](#2-singleton-pattern)
3. [Factory Pattern](#3-factory-pattern)
4. [Registry Pattern](#4-registry-pattern)
5. [Template Method Pattern](#5-template-method-pattern)
6. [Strategy Pattern](#6-strategy-pattern)
7. [Iterator Pattern](#7-iterator-pattern)
8. [Facade Pattern](#8-facade-pattern)
9. [Adapter Pattern](#9-adapter-pattern)
10. [Active Record Pattern](#10-active-record-pattern)

---

## 1. Observer Pattern

### Implementation: Hook System (Actions & Filters)

**Location:** `wp-includes/class-wp-hook.php`, `wp-includes/plugin.php`

**Purpose:** Allow plugins and themes to extend WordPress functionality without modifying core code.

**Structure:**
```
Subject (WordPress Core)
    │
    ├── notify() → do_action(), apply_filters()
    │
    └── Observers (Plugins/Themes)
         ├── subscribe() → add_action(), add_filter()
         └── unsubscribe() → remove_action(), remove_filter()
```

**Example:**
```php
// Core defines hook points
do_action('init');
$content = apply_filters('the_content', $content);

// Plugins/themes subscribe
add_action('init', 'my_init_function');
add_filter('the_content', 'my_content_filter');
```

**Key Classes:**
- `WP_Hook` - Observer collection and notification
- Global `$wp_filter` - Central hook storage

**Benefits:**
- Loose coupling between core and extensions
- Extensibility without code modification
- Plugin conflicts minimized

---

## 2. Singleton Pattern

### Implementation: Global Instances

**Locations:**
- `$wpdb` - Database connection
- `$wp_query` - Main query
- `$wp_rewrite` - Rewrite rules
- `$wp_roles` - Role definitions

**Purpose:** Ensure only one instance exists for shared resources.

**Example:**
```php
// wpdb is instantiated once in wp-settings.php
global $wpdb;
$wpdb = new wpdb(DB_USER, DB_PASSWORD, DB_NAME, DB_HOST);

// Used globally thereafter
$results = $wpdb->get_results("SELECT * FROM {$wpdb->posts}");
```

**Modern Singleton (Block Registry):**
```php
final class WP_Block_Type_Registry {
    private static $instance = null;
    
    public static function get_instance() {
        if (null === self::$instance) {
            self::$instance = new self();
        }
        return self::$instance;
    }
    
    private function __construct() {}
    private function __clone() {}
}
```

**Benefits:**
- Controlled access to shared resources
- Consistent state across application
- Memory efficiency

---

## 3. Factory Pattern

### Implementation: Post Type & Taxonomy Registration

**Location:** `wp-includes/post.php`, `wp-includes/taxonomy.php`

**Purpose:** Create objects with complex initialization logic.

**Example - Post Type Factory:**
```php
// Factory function creates WP_Post_Type objects
register_post_type('product', [
    'labels' => [...],
    'public' => true,
    'supports' => ['title', 'editor', 'thumbnail'],
]);

// Internally creates:
$post_type_object = new WP_Post_Type($post_type, $args);
$wp_post_types[$post_type] = $post_type_object;
```

**Example - Widget Factory:**
```php
// Widget Factory in WP_Widget_Factory
global $wp_widget_factory;
$wp_widget_factory->register('WP_Widget_Search');

// Factory method creates instance
public function register($widget) {
    $this->widgets[$widget] = new $widget();
}
```

**Benefits:**
- Encapsulates complex object creation
- Centralizes configuration validation
- Enables lazy instantiation

---

## 4. Registry Pattern

### Implementation: Block Type Registry

**Location:** `wp-includes/class-wp-block-type-registry.php`

**Purpose:** Central repository for registered items with lookup capability.

**Structure:**
```
WP_Block_Type_Registry (Singleton)
    │
    ├── register($name, $args)
    ├── unregister($name)
    ├── get_registered($name)
    ├── get_all_registered()
    └── is_registered($name)
```

**Example:**
```php
$registry = WP_Block_Type_Registry::get_instance();

// Register block type
$registry->register('core/paragraph', [
    'render_callback' => 'render_paragraph_block',
    'attributes' => [...]
]);

// Lookup
$block_type = $registry->get_registered('core/paragraph');
```

**Other Registries:**
- `WP_Block_Patterns_Registry`
- `WP_Block_Styles_Registry`
- `WP_Block_Pattern_Categories_Registry`
- `$wp_post_types` (array-based registry)
- `$wp_taxonomies` (array-based registry)

**Benefits:**
- Centralized management
- Prevents duplicate registration
- Easy lookup by identifier

---

## 5. Template Method Pattern

### Implementation: Walker Classes

**Location:** `wp-includes/class-walker.php`

**Purpose:** Define algorithm skeleton, let subclasses implement steps.

**Structure:**
```
WP_Walker (Abstract)
    │
    ├── walk() - Template method (final algorithm)
    ├── start_lvl() - Hook for subclass
    ├── end_lvl() - Hook for subclass
    ├── start_el() - Hook for subclass (abstract)
    └── end_el() - Hook for subclass
```

**Example:**
```php
abstract class Walker {
    // Template method - defines algorithm
    public function walk($elements, $max_depth, ...$args) {
        // Algorithm skeleton
        foreach ($elements as $element) {
            $this->display_element($element, ...);
        }
    }
    
    // Hooks for subclasses
    public function start_lvl(&$output, $depth, $args) {}
    public function end_lvl(&$output, $depth, $args) {}
    abstract public function start_el(&$output, $data_object, $depth, $args);
    public function end_el(&$output, $data_object, $depth, $args) {}
}

// Concrete implementation
class Walker_Nav_Menu extends Walker {
    public function start_el(&$output, $item, $depth, $args) {
        $output .= '<li class="menu-item">';
        $output .= '<a href="' . $item->url . '">' . $item->title . '</a>';
    }
}
```

**Implementations:**
- `Walker_Nav_Menu` - Navigation menus
- `Walker_Page` - Page listings
- `Walker_Category` - Category lists
- `Walker_Comment` - Comment threads

**Benefits:**
- Reusable algorithm structure
- Customization via inheritance
- Consistent traversal logic

---

## 6. Strategy Pattern

### Implementation: HTTP Transports

**Location:** `wp-includes/class-wp-http.php`

**Purpose:** Define family of algorithms, make them interchangeable.

**Structure:**
```
WP_Http (Context)
    │
    └── Uses one of:
         ├── WP_Http_Curl
         ├── WP_Http_Streams
         └── WP_Http_Fsockopen
```

**Example:**
```php
class WP_Http {
    // Select strategy based on environment
    public function _dispatch_request($url, $args) {
        // Try cURL first
        if (WP_Http_Curl::test()) {
            return (new WP_Http_Curl())->request($url, $args);
        }
        
        // Fall back to streams
        if (WP_Http_Streams::test()) {
            return (new WP_Http_Streams())->request($url, $args);
        }
        
        // Last resort: fsockopen
        return (new WP_Http_Fsockopen())->request($url, $args);
    }
}
```

**Other Strategy Uses:**
- Image editors: `WP_Image_Editor_GD`, `WP_Image_Editor_Imagick`
- Filesystem: `WP_Filesystem_Direct`, `WP_Filesystem_FTPext`

**Benefits:**
- Runtime algorithm selection
- Easy to add new strategies
- Encapsulates implementation details

---

## 7. Iterator Pattern

### Implementation: The Loop & Block Lists

**Location:** `wp-includes/class-wp-query.php`, `wp-includes/class-wp-block-list.php`

**Purpose:** Sequential access to collection elements.

### The Loop

```php
// WP_Query provides iteration interface
if ($query->have_posts()) {
    while ($query->have_posts()) {
        $query->the_post();
        // Access current post via globals
        the_title();
        the_content();
    }
}
```

**WP_Query Iterator Methods:**
- `have_posts()` - Check if more items
- `the_post()` - Advance to next item
- `rewind_posts()` - Reset to beginning

### Block List Iterator

```php
class WP_Block_List implements Iterator, ArrayAccess, Countable {
    public function rewind() { ... }
    public function current() { ... }
    public function key() { ... }
    public function next() { ... }
    public function valid() { ... }
}

// Usage
foreach ($block->inner_blocks as $inner_block) {
    echo $inner_block->render();
}
```

**Benefits:**
- Uniform traversal interface
- Encapsulates iteration logic
- Supports multiple iteration methods

---

## 8. Facade Pattern

### Implementation: Template Tags & API Functions

**Purpose:** Provide simplified interface to complex subsystem.

**Examples:**

```php
// Facade function hides WP_Query complexity
$posts = get_posts(['post_type' => 'product', 'posts_per_page' => 10]);

// Facade function hides wpdb complexity
$post = get_post(123);

// Facade function hides wp_insert_post complexity
$id = wp_insert_post([
    'post_title' => 'My Post',
    'post_content' => 'Content...',
    'post_status' => 'publish'
]);

// Facade function hides user capability checking
if (current_user_can('edit_posts')) {
    // ...
}
```

**Common Facades:**
| Facade Function | Underlying System |
|-----------------|-------------------|
| `get_posts()` | `WP_Query` |
| `get_post()` | `WP_Post::get_instance()` + cache |
| `get_user_by()` | `WP_User` + cache |
| `get_terms()` | `WP_Term_Query` |
| `wp_remote_get()` | `WP_Http` |
| `wp_mail()` | PHPMailer |

**Benefits:**
- Simpler API for common operations
- Backward compatibility
- Encapsulates complexity

---

## 9. Adapter Pattern

### Implementation: Database Abstraction

**Location:** `wp-includes/class-wpdb.php`

**Purpose:** Wrap incompatible interface to provide expected interface.

**Structure:**
```
Application Code
    │
    ▼
  wpdb (Adapter)
    │
    ├── Adapts mysqli to WordPress API
    │
    ▼
  mysqli extension
```

**Example:**
```php
class wpdb {
    // Adapts mysqli to WordPress-friendly API
    public function get_results($query, $output = OBJECT) {
        // Execute via mysqli
        $this->query($query);
        
        // Adapt result format
        if ($output === OBJECT) {
            return $this->last_result;
        } elseif ($output === ARRAY_A) {
            return array_map(fn($row) => (array)$row, $this->last_result);
        }
        // ...
    }
    
    // Adapts parameter binding
    public function prepare($query, ...$args) {
        // Convert %d, %s, %f placeholders
        // to properly escaped values
    }
}
```

**Benefits:**
- Database portability (theoretical)
- Consistent API regardless of driver
- Centralized escaping/sanitization

---

## 10. Active Record Pattern

### Implementation: Entity Classes

**Locations:** `WP_Post`, `WP_User`, `WP_Term`, `WP_Comment`

**Purpose:** Object wraps database row, provides CRUD methods.

**Partial Implementation:**
WordPress entity classes are *partial* Active Record implementations:

```php
class WP_Post {
    // Properties map to database columns
    public $ID;
    public $post_author;
    public $post_date;
    public $post_content;
    public $post_title;
    // ...
    
    // Read operation (static factory)
    public static function get_instance($post_id) {
        // Fetches from database
        global $wpdb;
        $post = $wpdb->get_row("SELECT * FROM {$wpdb->posts} WHERE ID = %d", $post_id);
        return new WP_Post($post);
    }
    
    // Note: No save() or delete() methods
    // Updates done via wp_update_post() facade
}
```

**Full CRUD via Facades:**
```php
// Create
$id = wp_insert_post([...]);

// Read
$post = get_post($id);

// Update
wp_update_post(['ID' => $id, 'post_title' => 'New Title']);

// Delete
wp_delete_post($id);
```

**Benefits:**
- Object-oriented data access
- Encapsulates data transformation
- Caching integration

---

## Pattern Interaction Diagram

```
                    ┌─────────────────────────────┐
                    │         User Request        │
                    └─────────────┬───────────────┘
                                  │
                    ┌─────────────▼───────────────┐
                    │    Facade (Template Tags)   │
                    │  get_posts(), the_content() │
                    └─────────────┬───────────────┘
                                  │
          ┌───────────────────────┼───────────────────────┐
          │                       │                       │
┌─────────▼─────────┐   ┌────────▼────────┐   ┌──────────▼──────────┐
│  Observer Pattern │   │ Iterator Pattern │   │   Factory Pattern   │
│   (Hook System)   │   │   (The Loop)     │   │ (register_post_type)│
│                   │   │                  │   │                     │
│ apply_filters()   │   │ have_posts()     │   │ Creates WP_Post_Type│
│ do_action()       │   │ the_post()       │   │                     │
└─────────┬─────────┘   └────────┬────────┘   └──────────┬──────────┘
          │                      │                       │
          │             ┌────────▼────────┐              │
          │             │  Active Record  │              │
          │             │ WP_Post, WP_User│              │
          │             └────────┬────────┘              │
          │                      │                       │
          └───────────────────┬──┴───────────────────────┘
                              │
                    ┌─────────▼─────────┐
                    │  Adapter Pattern  │
                    │      (wpdb)       │
                    └─────────┬─────────┘
                              │
                    ┌─────────▼─────────┐
                    │     Database      │
                    └───────────────────┘
```

---

## Summary Table

| Pattern | WordPress Implementation | Primary Benefit |
|---------|-------------------------|-----------------|
| Observer | Hooks (WP_Hook) | Extensibility |
| Singleton | $wpdb, Registries | Resource control |
| Factory | register_post_type() | Complex creation |
| Registry | Block Type Registry | Centralized lookup |
| Template Method | Walker classes | Algorithm reuse |
| Strategy | HTTP/Image transports | Runtime flexibility |
| Iterator | The Loop, Block List | Uniform traversal |
| Facade | Template tags | Simplified API |
| Adapter | wpdb | Interface compatibility |
| Active Record | WP_Post, WP_User | OO data access |
