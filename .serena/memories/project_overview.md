# WordPress Core Repository Overview

## Project Purpose

WordPress is a semantic personal publishing platform - a free and open-source content management system (CMS) written in PHP. It powers over 40% of the web and is designed for building websites, blogs, and web applications.

## Version Information

- **Current Version**: 7.0-alpha-61365 (development version)
- **Database Version**: 60717
- **TinyMCE Version**: 49110-20250317

## System Requirements

### Minimum
- PHP: 7.2.24 or greater
- MySQL: 5.5.5 or greater
- Required PHP Extensions: `json`, `hash`

### Recommended
- PHP: 8.3 or greater
- MySQL: 8.0 or greater / MariaDB: 10.6 or greater
- Apache mod_rewrite module
- HTTPS support

## Technology Stack

- **Primary Language**: PHP
- **Database**: MySQL/MariaDB
- **Server**: Apache (recommended), Nginx
- **Frontend**: JavaScript, jQuery, CSS
- **Editor**: TinyMCE (classic), Gutenberg (blocks)
- **Templating**: PHP templates

## License

GNU General Public License (GPL) version 2 or later

## Key Features

1. **Plugin API**: Extensible architecture via hooks (actions and filters)
2. **Theme System**: Customizable presentation layer
3. **REST API**: RESTful API for headless CMS and external integrations
4. **Multisite**: Network of multiple WordPress sites
5. **Block Editor (Gutenberg)**: Modern block-based content editing
6. **User Management**: Roles and capabilities system
7. **Media Library**: Built-in media management
8. **Customizer**: Live preview theme customization
