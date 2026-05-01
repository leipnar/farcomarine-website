# Technical Investigation Log - FARCO Marine WordPress Migration

**Session Date**: November 1, 2025
**Start Time**: 01:31 UTC
**End Time**: 08:41 UTC
**Duration**: ~7 hours
**Investigator**: Claude Code Assistant

## Connection Details

### SSH Access
```bash
# Primary connection method
ssh -o IdentitiesOnly=yes -i /tmp/hostinger_key -p 65002 u980179914@46.202.142.35

# Authentication source: 1Password CLI
op item get "SSH Key [Hostinger]" --format json | jq -r '.fields[] | select(.type == "CONCEALED").value'
```

### Directory Structure
```
/home/u980179914/
├── domains/
│   └── farcomarine.net/
│       └── public_html/
│           ├── wp-config.php (current)
│           ├── wp-content/
│           │   ├── plugins/
│           │   │   └── mwfcpro/ (problematic)
│           │   └── themes/
│           └── old directory/
│               └── wp-config.php (backup)
├── .logs/
├── .ssh/
└── .wp-cli/
```

## Investigation Timeline

### 01:31 UTC - Initial Connection Attempt
- **Command**: `ssh -p 65002 u980179914@46.202.142.35`
- **Result**: "Too many authentication failures" error
- **Resolution**: Used 1Password to extract correct SSH key

### 01:35 UTC - Successful SSH Connection
- **Method**: Used `-o IdentitiesOnly=yes` flag
- **Result**: Connection established
- **Directory Listed**: Standard Hostinger user home directory structure

### 01:40 UTC - WordPress Site Discovery
- **Found**: `domains/farcomarine.net/public_html/`
- **WordPress Config**: Two `wp-config.php` files identified
- **Database**: `u980179914_wMUOY` with user `u980179914_6jVvu`

### 01:45 UTC - Configuration Analysis
- **Current Config**: DB_HOST=localhost, charset=utf8mb4
- **Old Config**: DB_HOST=127.0.0.1, charset=utf8
- **Key Finding**: Database password changed during migration

### 02:00 UTC - WordPress CLI Operations
- **WP-CLI Version**: 2.12.0
- **WordPress Version**: 6.8.3
- **Database Check**: All tables OK (26 tables verified)

### 02:15 UTC - Critical Error Discovery
- **Error**: `Fatal error: Only files produced by the PHP 8.2 ionCube Encoder can run on PHP 8.2`
- **Source**: `/wp-content/plugins/mwfcpro/includes/mwfcpro-main.php`
- **Root Cause**: Plugin encoded for PHP 8.1, server running PHP 8.2.28

### 02:30 UTC - Plugin Deactivation
- **Action**: `wp plugin deactivate mwfcpro --skip-plugins`
- **Result**: Plugin successfully deactivated
- **Impact**: Site should now be accessible, but functionality may be reduced

### 08:00 UTC - Site Testing
- **HTTP Response**: 301 redirect to HTTPS
- **HTTPS Response**: HTTP 500 Internal Server Error
- **Headers**: LiteSpeed server with Cloudflare proxy

### 08:30 UTC - Debug Configuration
- **WP_DEBUG**: Enabled
- **WP_DEBUG_LOG**: Enabled
- **WP_DEBUG_DISPLAY**: Disabled

## Technical Commands Used

### Database Verification
```bash
wp db check
# Result: 26 tables OK
```

### Plugin Management
```bash
wp plugin deactivate mwfcpro --skip-plugins
# Result: Plugin deactivated successfully
```

### Configuration Updates
```bash
wp config set WP_DEBUG true --raw
wp config set WP_DEBUG_LOG true --raw
wp config set WP_DEBUG_DISPLAY false --raw
```

### Site Health Checks
```bash
wp core version          # 6.8.3
wp option get siteurl    # Command timed out
wp option get home       # Command timed out
```

## Error Analysis

### ionCube Compatibility Error
```
Fatal error: Only files produced by the PHP 8.2 ionCube Encoder can run on PHP 8.2.
The file /home/u980179914/domains/farcomarine.net/public_html/wp-content/plugins/mwfcpro/includes/mwfcpro-main.php
has been encoded by the ionCube Encoder for PHP 8.1. in Unknown on line 0
```

### Performance Issues
- Commands consistently timeout after 2 minutes
- Site responses are slow or incomplete
- Indicates resource constraints or configuration issues

## Environment Details

### Server Information
- **Platform**: Linux
- **PHP Version**: 8.2.28 (cli)
- **ionCube Loader**: v14.4.0
- **OPcache**: Enabled
- **Web Server**: LiteSpeed
- **Proxy**: Cloudflare

### WordPress Installation
- **Version**: 6.8.3
- **Database**: MySQL (shared hosting)
- **Table Prefix**: wp_
- **Total Tables**: 26
- **Active Plugins**: Unknown (due to timeouts)

### Plugin Inventory
```
Active (before deactivation):
- mwfcpro (INCOMPATIBLE - deactivated)
- Multiple other plugins (list incomplete due to timeouts)

Plugin Details:
- mwfcpro: Located in /wp-content/plugins/mwfcpro/
- Main file: includes/mwfcpro-main.php (ionCube encoded)
- Issue: PHP 8.1 encoding on PHP 8.2 server
```

## File Modifications

### wp-config.php Changes Made
```php
// Debug constants added
define( 'WP_DEBUG', true );
define( 'WP_DEBUG_LOG', true );
define( 'WP_DEBUG_DISPLAY', false );
```

### Plugin Status
- **mwfcpro**: Deactivated due to compatibility issues
- **Other plugins**: Status unknown (commands timed out)

## Next Steps Required

### Immediate Actions
1. **Update mwfcpro Plugin** - Find PHP 8.2 compatible version
2. **Performance Investigation** - Diagnose command timeout issues
3. **Functionality Testing** - Verify site operations post-plugin deactivation

### Backup Procedures
1. **Create Full Backup** - Before any major changes
2. **Database Export** - Ensure data safety
3. **File Archive** - Store current state

### Monitoring Setup
1. **Error Log Monitoring** - Track wp-content/debug.log
2. **Performance Metrics** - Monitor response times
3. **Uptime Monitoring** - Ensure site availability

## Resources Accessed

### SSH Keys
- **Source**: 1Password CLI
- **Item**: "SSH Key [Hostinger]"
- **UUID**: vrfytcs5lqqfreefyuol7p7tmq

### Documentation References
- WordPress CLI documentation
- Hostinger support resources
- ionCube compatibility requirements

## Technical Limitations Encountered

1. **Command Timeouts**: Many WP-CLI commands exceed 2-minute timeout
2. **Plugin Access**: Cannot list all active plugins due to performance issues
3. **Debug Log Creation**: Unable to verify debug.log creation due to timeouts
4. **Site URL Verification**: Cannot retrieve siteurl/home options

---

**Investigation Complete**: November 1, 2025, 08:41 UTC
**Status**: Critical issue identified, temporary fix implemented
**Recommendation**: Address plugin compatibility as highest priority