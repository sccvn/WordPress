# Suggested Commands for WordPress Development

## System Commands (Linux)

```bash
# Navigation
cd /path/to/wordpress
ls -la
pwd

# File operations
cat filename.php
grep -r "search_term" .
find . -name "*.php"

# Git
git status
git diff
git log --oneline -10
git branch -a
git checkout <branch>
```

## Local Development Setup

### Using Docker (Recommended)
```bash
# Quick setup with wp-env (official WordPress tool)
npm -g install @wordpress/env
wp-env start

# Docker Compose (manual)
docker-compose up -d
```

### Using PHP Built-in Server
```bash
# Start local server
php -S localhost:8080

# With custom document root
php -S localhost:8080 -t /path/to/wordpress
```

### Using WP-CLI (Command Line Interface)
```bash
# Install WP-CLI
curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar
chmod +x wp-cli.phar
sudo mv wp-cli.phar /usr/local/bin/wp

# Common WP-CLI commands
wp core download           # Download WordPress
wp config create           # Create wp-config.php
wp db create              # Create database
wp core install           # Install WordPress
wp plugin list            # List plugins
wp theme list             # List themes
wp user list              # List users
wp option get <option>    # Get option value
wp cache flush            # Clear cache
wp rewrite flush          # Flush rewrite rules
```

## Code Quality Tools

### PHP CodeSniffer (WordPress Coding Standards)
```bash
# Install PHPCS with WordPress standards
composer require --dev wp-coding-standards/wpcs
composer require --dev dealerdirect/phpcodesniffer-composer-installer

# Run coding standards check
./vendor/bin/phpcs --standard=WordPress path/to/file.php
./vendor/bin/phpcs --standard=WordPress-Core path/to/file.php
./vendor/bin/phpcs --standard=WordPress-Extra path/to/file.php

# Auto-fix issues
./vendor/bin/phpcbf --standard=WordPress path/to/file.php
```

### PHPStan (Static Analysis)
```bash
# Install
composer require --dev phpstan/phpstan
composer require --dev szepeviktor/phpstan-wordpress

# Run analysis
./vendor/bin/phpstan analyse wp-includes/functions.php
```

### PHPMD (PHP Mess Detector)
```bash
composer require --dev phpmd/phpmd
./vendor/bin/phpmd path/to/file.php text cleancode,codesize,controversial
```

## Testing

### PHPUnit (WordPress uses PHPUnit for unit tests)
```bash
# The official WordPress test suite
svn co https://develop.svn.wordpress.org/trunk/ wordpress-develop
cd wordpress-develop
npm install
npm run build:dev
npm run test:php

# Run specific test file
./vendor/bin/phpunit tests/phpunit/tests/post.php

# Run tests matching pattern
./vendor/bin/phpunit --filter test_function_name
```

### JavaScript Testing
```bash
# From WordPress develop repo
npm run test:js
npm run test:unit
```

## Database Operations

```bash
# MySQL CLI
mysql -u root -p wordpress

# Export database
mysqldump -u root -p wordpress > backup.sql

# Import database
mysql -u root -p wordpress < backup.sql

# Using WP-CLI
wp db export backup.sql
wp db import backup.sql
wp db query "SELECT * FROM wp_options LIMIT 10"
```

## Debugging

### Enable Debug Mode (wp-config.php)
```php
define( 'WP_DEBUG', true );
define( 'WP_DEBUG_LOG', true );     // Log to wp-content/debug.log
define( 'WP_DEBUG_DISPLAY', false ); // Don't show on screen
define( 'SCRIPT_DEBUG', true );     // Use non-minified JS/CSS
define( 'SAVEQUERIES', true );      // Log all database queries
```

### View Debug Log
```bash
tail -f wp-content/debug.log
cat wp-content/debug.log | grep "PHP Fatal"
```

## Build Tools (WordPress Develop)

```bash
# Clone develop repository
git clone https://github.com/WordPress/wordpress-develop.git
cd wordpress-develop

# Install dependencies
npm install
composer install

# Build
npm run build
npm run build:dev   # Development build

# Watch for changes
npm run dev

# Linting
npm run lint:php    # PHP linting
npm run lint:js     # JavaScript linting
npm run lint:css    # CSS linting

# Formatting
npm run format:js   # Format JavaScript
```

## Gutenberg Development

```bash
# Install Gutenberg plugin from npm
npm install @wordpress/scripts --save-dev

# Create a block
npx @wordpress/create-block my-block

# Build block assets
npm run build
npm run start  # Watch mode
```
