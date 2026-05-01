# FARCO Marine WordPress Website Project

**Project**: FARCO Marine Website Migration and Maintenance
**Site**: [farcomarine.net](https://farcomarine.net)
**Status**: 🟡 Partially Functional - Critical Plugin Issue Identified
**Last Updated**: November 1, 2025

## Project Overview

This repository contains documentation and development resources for the FARCO Marine WordPress website migration project. The site has been migrated to Hostinger hosting and is currently experiencing technical issues that require immediate attention.

## Current Status

### 🚨 Critical Issues
- **Site Accessibility**: HTTP 500 errors due to plugin incompatibility
- **Plugin Issue**: `mwfcpro` plugin incompatible with PHP 8.2
- **Performance**: Commands timing out, indicating performance bottlenecks

### ✅ Recently Resolved
- SSH access established and verified
- Database integrity confirmed (all tables healthy)
- ionCube compatibility issue identified and plugin deactivated
- WordPress debugging enabled

### 📋 Current Configuration
- **WordPress Version**: 6.8.3
- **PHP Version**: 8.2.28
- **Hosting**: Hostinger (shared)
- **Database**: MySQL (healthy, 26 tables)

## Quick Actions

### Immediate Actions Required

1. **Check Site Status**
   ```bash
   ssh -o IdentitiesOnly=yes -i /path/to/hostinger_key -p 65002 u980179914@46.202.142.35
   cd domains/farcomarine.net/public_html
   curl -I https://farcomarine.net
   ```

2. **Verify Plugin Compatibility**
   ```bash
   wp plugin list --status=active
   wp plugin update --dry-run --all
   ```

3. **Create Emergency Backup**
   ```bash
   wp db export emergency_backup_$(date +%Y%m%d_%H%M%S).sql
   tar -czf files_emergency_backup_$(date +%Y%m%d_%H%M%S).tar.gz wp-content/
   ```

## Documentation Structure

### 📁 `/docs/` Directory
- **[MIGRATION_ANALYSIS_REPORT.md](docs/MIGRATION_ANALYSIS_REPORT.md)** - Complete analysis of migration issues
- **[TECHNICAL_INVESTIGATION_LOG.md](docs/TECHNICAL_INVESTIGATION_LOG.md)** - Detailed technical investigation timeline
- **[DEVELOPMENT_GUIDE.md](docs/DEVELOPMENT_GUIDE.md)** - Comprehensive development and maintenance guide
- **[LLM_MODEL_NOTES.md](docs/LLM_MODEL_NOTES.md)** - AI assistant analysis and recommendations
- **[FUTURE_TODOS_AND_ACTION_ITEMS.md](docs/FUTURE_TODOS_AND_ACTION_ITEMS.md)** - Prioritized action items and roadmap

## Server Access Information

### SSH Connection
```bash
# Primary connection
ssh -o IdentitiesOnly=yes -i /path/to/hostinger_key -p 65002 u980179914@46.202.142.35

# Server details
- Host: 46.202.142.35
- Port: 65002
- User: u980179914
- SSH Key: Stored in 1Password under "SSH Key [Hostinger]"
```

### WordPress Installation Path
```
/home/u980179914/domains/farcomarine.net/public_html/
```

### Database Configuration
- **Database**: u980179914_wMUOY
- **Username**: u980179914_6jVvu
- **Password**: K6w1UcoGR9
- **Host**: localhost

## Key Findings from Migration Analysis

### Configuration Changes
| Parameter | Old Value | New Value | Impact |
|-----------|-----------|-----------|--------|
| DB_PASSWORD | ZoW8y16q0j | K6w1UcoGR9 | 🔴 Updated |
| DB_HOST | 127.0.0.1 | localhost | 🟡 Minor |
| DB_CHARSET | utf8 | utf8mb4 | 🟡 Improved |
| PHP_VERSION | Unknown | 8.2.28 | 🔴 Compatibility issue |

### Critical Plugin Issue
- **Plugin**: mwfcpro
- **Issue**: ionCube encoded for PHP 8.1, server runs PHP 8.2
- **Status**: Deactivated (temporary fix)
- **Priority**: HIGH - May affect core functionality

## Immediate Next Steps

1. **🚨 Critical (24-48 hours)**
   - [ ] Verify site accessibility after plugin deactivation
   - [ ] Research mwfcpro plugin alternatives or updates
   - [ ] Create full backup before further changes

2. **🔴 High Priority (1 week)**
   - [ ] Complete plugin and theme audit
   - [ ] Investigate performance issues
   - [ ] Conduct security audit

3. **🟡 Medium Priority (2-4 weeks)**
   - [ ] Set up automated backup system
   - [ ] Implement monitoring and alerting
   - [ ] Create staging environment

## Development Workflow

### Before Making Changes
1. Create backup
2. Test in staging (if available)
3. Review impact on functionality
4. Document changes

### After Making Changes
1. Verify site functionality
2. Monitor error logs
3. Update documentation
4. Communicate changes to team

## Troubleshooting Commands

### Common Issues
```bash
# Check site status
wp site status
wp health check

# Database issues
wp db check
wp db repair
wp db optimize

# Plugin issues
wp plugin deactivate --all
wp plugin activate [plugin-name]

# Performance issues
wp cache flush
wp transient delete --all
```

## Support and Resources

### Hosting Provider
- **Provider**: Hostinger
- **Panel**: hpanel.hostinger.com
- **Support**: 24/7 available

### Technical Documentation
- WordPress Codex: [wordpress.org](https://wordpress.org)
- WP-CLI Documentation: [wp-cli.org](https://wp-cli.org)
- Hostinger Knowledge Base: [support.hostinger.com](https://support.hostinger.com)

### Emergency Contacts
- **Critical Issues**: Hostinger 24/7 Support
- **Development**: Technical Lead
- **Security**: Security Team

## Project History

### Migration Timeline
- **Pre-Migration**: Site hosted on previous provider
- **Migration Date**: Recent (exact date TBD)
- **Issues Discovered**: November 1, 2025
- **Initial Fix Applied**: Plugin deactivation

### Major Changes During Migration
- Database credentials updated
- PHP version upgraded to 8.2.28
- Database charset improved to utf8mb4
- Hosting provider changed to Hostinger

## Contributing Guidelines

### Documentation Updates
1. Update relevant documents after changes
2. Include date and author information
3. Use clear, concise language
4. Test commands before inclusion

### Issue Reporting
1. Use priority levels (Critical, High, Medium, Low)
2. Include detailed steps to reproduce
3. Provide expected vs actual results
4. Include any error messages or logs

## License and Usage

This project documentation is for internal use by the FARCO Marine development team and authorized contractors. Redistribution is not permitted without explicit permission.

---

**For immediate assistance with critical issues, contact the technical lead or hosting provider support.**