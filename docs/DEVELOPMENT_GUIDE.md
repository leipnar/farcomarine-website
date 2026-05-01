# FARCO Marine WordPress Development Guide

**Last Updated**: November 1, 2025
**Version**: 1.0
**Site**: farcomarine.net
**Hosting**: Hostinger

## Project Overview

This guide provides comprehensive instructions for maintaining, troubleshooting, and developing the FARCO Marine WordPress website following the recent migration to Hostinger hosting.

## Environment Setup

### Server Access

#### SSH Connection
```bash
# Primary connection method
ssh -o IdentitiesOnly=yes -i /path/to/hostinger_key -p 65002 u980179914@46.202.142.35

# SSH Key Management
# Key is stored in 1Password under "SSH Key [Hostinger]"
# Extract using: op item get "SSH Key [Hostinger]" --format json
```

#### Directory Structure
```
/home/u980179914/domains/farcomarine.net/public_html/
├── wp-admin/           # WordPress admin interface
├── wp-content/         # Themes, plugins, uploads
│   ├── plugins/        # WordPress plugins
│   ├── themes/         # WordPress themes
│   └── uploads/        # Media files
├── wp-includes/        # WordPress core files
├── wp-config.php       # Main configuration file
├── .htaccess          # Apache/Nginx configuration
└── old directory/      # Backup of previous installation
```

### WordPress CLI (WP-CLI)

#### Basic Commands
```bash
# Navigate to WordPress directory
cd domains/farcomarine.net/public_html

# Check WordPress version
wp core version

# Check database status
wp db check

# Update WordPress core
wp core update

# Plugin management
wp plugin list                    # List all plugins
wp plugin status                  # Check plugin status
wp plugin update [plugin-name]    # Update specific plugin
wp plugin activate [plugin-name]  # Activate plugin
wp plugin deactivate [plugin-name] # Deactivate plugin

# Theme management
wp theme list
wp theme status
wp theme update [theme-name]

# Database operations
wp db export backup.sql           # Export database
wp db import backup.sql           # Import database
wp db optimize                    # Optimize database
```

## Configuration Management

### wp-config.php Critical Settings
```php
/** Database Configuration */
define( 'DB_NAME', 'u980179914_wMUOY' );
define( 'DB_USER', 'u980179914_6jVvu' );
define( 'DB_PASSWORD', 'K6w1UcoGR9' );
define( 'DB_HOST', 'localhost' );

/** WordPress URLs */
define( 'WP_HOME', 'https://farcomarine.net' );
define( 'WP_SITEURL', 'https://farcomarine.net' );

/** Debug Settings (Development Only) */
define( 'WP_DEBUG', true );           // Set to false in production
define( 'WP_DEBUG_LOG', true );
define( 'WP_DEBUG_DISPLAY', false );

/** Auto Updates */
define( 'WP_AUTO_UPDATE_CORE', true );

/** File System Method */
define( 'FS_METHOD', 'direct' );
```

## Plugin Management

### Critical Plugins

#### mwfcpro Plugin (⚠️ CRITICAL ATTENTION NEEDED)
- **Status**: Currently deactivated due to PHP 8.2 incompatibility
- **Issue**: ionCube encoded for PHP 8.1, server runs PHP 8.2.28
- **Location**: `/wp-content/plugins/mwfcpro/`
- **Priority**: HIGH - May contain essential business functionality

#### Plugin Update Procedures
```bash
# Before updating any plugin:
1. Create full backup
2. Test in staging environment
3. Check plugin compatibility with PHP 8.2

# Safe update process:
wp plugin update [plugin-name] --dry-run  # Preview update
wp plugin update [plugin-name]            # Perform update
```

#### Plugin Compatibility Checks
- Always verify PHP version compatibility
- Check WordPress version requirements
- Review plugin changelog for breaking changes
- Test functionality after updates

## Database Management

### Database Connection Details
- **Database Name**: u980179914_wMUOY
- **Username**: u980179914_6jVvu
- **Password**: K6w1UcoGR9
- **Host**: localhost
- **Charset**: utf8mb4

### Database Operations
```bash
# Create backup
wp db export --add-drop-table backup_$(date +%Y%m%d_%H%M%S).sql

# Check table health
wp db check

# Optimize database
wp db optimize

# Search and replace (use with caution)
wp search-replace 'old-domain.com' 'new-domain.com'

# Reset admin password
wp user update admin --user_pass=newpassword
```

## Performance Optimization

### Caching Configuration
The site uses LiteSpeed cache plugin. Key settings:
- **Cache TTL**: Configure appropriately for content update frequency
- **Cache Purge**: Set up automatic purge on content updates
- **Browser Caching**: Enabled via .htaccess rules

### Database Optimization
```bash
# Clean up revisions
wp post delete $(wp post list --post_type=revision --format=ids)

# Clean up spam comments
wp comment delete $(wp comment list --status=spam --format=ids)

# Optimize tables
wp db optimize
```

## Security Best Practices

### Regular Security Tasks
1. **Keep WordPress Core Updated**
   ```bash
   wp core check-update
   wp core update
   ```

2. **Plugin and Theme Updates**
   ```bash
   wp plugin update --all
   wp theme update --all
   ```

3. **File Permissions**
   ```bash
   # Recommended permissions
   chmod 755 wp-content
   chmod 755 wp-admin
   chmod 755 wp-includes
   chmod 644 wp-config.php
   ```

4. **Security Hardening**
   - Disable file editing in WordPress admin
   - Use strong passwords
   - Implement 2FA where possible
   - Regular security scans

## Backup and Recovery

### Automated Backup Strategy
```bash
# Create comprehensive backup script
#!/bin/bash
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/home/u980179914/backups"

# Create backup directory
mkdir -p $BACKUP_DIR

# Database backup
wp db export $BACKUP_DIR/db_backup_$DATE.sql

# Files backup
tar -czf $BACKUP_DIR/files_backup_$DATE.tar.gz wp-content/

# Keep last 7 days of backups
find $BACKUP_DIR -name "*.sql" -mtime +7 -delete
find $BACKUP_DIR -name "*.tar.gz" -mtime +7 -delete
```

### Recovery Procedures
```bash
# Database restoration
wp db import backup_file.sql

# File restoration
tar -xzf files_backup.tar.gz

# Verify site functionality
wp site status
```

## Troubleshooting Guide

### Common Issues and Solutions

#### 1. HTTP 500 Internal Server Error
```bash
# Check error logs
tail -f wp-content/debug.log

# Check .htaccess file
mv .htaccess .htaccess.backup
# Generate new .htaccess
wp rewrite flush

# Deactivate all plugins
wp plugin deactivate --all

# Reactivate plugins one by one
wp plugin activate [plugin-name]
```

#### 2. White Screen of Death
```bash
# Enable debug mode
wp config set WP_DEBUG true --raw
wp config set WP_DEBUG_DISPLAY true --raw

# Check PHP error log
tail -f /path/to/php/error.log

# Increase memory limit
wp config set WP_MEMORY_LIMIT '256M'
```

#### 3. Database Connection Errors
```bash
# Verify database credentials
wp db check

# Check database server status
wp db cli "SHOW STATUS;"

# Repair database if needed
wp db repair
```

#### 4. Plugin Update Failures
```bash
# Clear plugin cache
wp cache flush

# Manually update plugin
rm -rf wp-content/plugins/plugin-name
# Upload new version via FTP or wp-cli
wp plugin install plugin-name.zip
```

## Development Workflow

### Local Development Setup
1. Set up local WordPress environment
2. Pull database from production (with sanitization)
3. Sync media files
4. Configure local wp-config.php
5. Set up version control (git)

### Staging Environment
- Create staging subdomain
- Mirror production configuration
- Test all updates before deployment
- Use for client approval

### Deployment Process
1. **Pre-deployment Checklist**
   - [ ] Full backup created
   - [ ] All changes tested in staging
   - [ ] Plugin compatibility verified
   - [ ] Database backed up

2. **Deployment Steps**
   - Update WordPress core
   - Update plugins and themes
   - Update custom code
   - Test functionality
   - Monitor for errors

## Monitoring and Maintenance

### Daily Tasks
- Check site availability
- Monitor error logs
- Verify backups are running

### Weekly Tasks
- Update plugins and themes
- Check for security vulnerabilities
- Review performance metrics

### Monthly Tasks
- Full security audit
- Database optimization
- Performance review
- Backup verification

## Contact Information

### Hosting Provider
- **Provider**: Hostinger
- **Panel**: hpanel.hostinger.com
- **Support**: Available 24/7

### Domain Registrar
- **Domain**: farcomarine.net
- **Registrar**: Through Hostinger

### Technical Contacts
- **Server Admin**: u980179914@46.202.142.35
- **SSH Access**: Via 1Password-managed keys

## Appendix

### Useful WP-CLI Commands
```bash
# Generate new salts
wp config shuffle-salts

# Clean transients
wp transient delete --all

# Regenerate thumbnails
wp media regenerate

# Check site health
wp site health

# Export content
wp export --all --dir=/path/to/exports
```

### Cron Job Management
```bash
# List WordPress cron jobs
wp cron event list

# Test WordPress cron
wp cron event run --due-now

# Disable WordPress cron (if using system cron)
wp config set DISABLE_WP_CRON true --raw
```

---

**This guide should be updated after any major changes to the site configuration or hosting environment.**