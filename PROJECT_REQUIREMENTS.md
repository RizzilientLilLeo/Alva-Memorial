# Alva-Memorial Project Requirements

> This document consolidates all project requirements gathered from GitHub issues, pull requests, and discussions to help synchronize work between GitHub Mobile, Web Interface, and Copilot.

## Project Overview

The Alva-Memorial repository is a tribute to Alva Johnson, a beloved Jamaican community leader from Buffalo. The project aims to expand from a simple memorial website into a more comprehensive platform with additional backend services.

---

## Current State

### Completed Work
- ✅ Static HTML memorial page (`index.html`) with elegant design
- ✅ Responsive layout using CSS Grid and Flexbox
- ✅ Google Fonts integration (Cormorant Garamond)
- ✅ Photo gallery structure (placeholders for additional photos)
- ✅ GitHub Pages deployment enabled

### In Progress
- 🔄 **PR #5**: Copilot instructions setup (Draft - linked to Issue #4)
  - Creates `.github/copilot-instructions.md`
  - Provides context for AI-assisted development

---

## Open Requirements (GitHub Issues)

### Issue #1: Create Localized, Secure, and Anonymous Python Web Server

**Status**: Open  
**Labels**: enhancement  
**Priority**: High

#### Requirements Summary
A Python-based web server that is:
- **Localized**: Supports multiple languages and regional formats
- **Secure**: Implements modern security best practices
- **Anonymous**: Protects user identities and minimizes tracking

#### Technical Specifications
| Component | Requirement |
|-----------|-------------|
| Framework | Flask or FastAPI (recommended) |
| i18n/l10n | Internationalization/localization support |
| Security | TLS/SSL, strong authentication, input sanitization |
| Privacy | Minimal data retention, privacy-focused logging |
| Optional | Tor integration for anonymity |

#### Acceptance Criteria
- [ ] HTTPS support with TLS/SSL
- [ ] User authentication system
- [ ] Input validation and sanitization
- [ ] Multi-language support (i18n)
- [ ] Regional format handling (dates, numbers, currencies)
- [ ] Privacy-compliant logging
- [ ] Security audit documentation

---

### Issue #2: MDM Web Server Implementation with Secure URL Shortener

**Status**: Open  
**Labels**: documentation, enhancement  
**Priority**: High

#### Proposed Server Structure
```
mdm_server/
├── app/
│   ├── __init__.py
│   ├── routes.py
│   ├── models.py
│   ├── utils/
│   │   ├── __init__.py
│   │   ├── url_shortener.py
│   │   └── mdm_handler.py
│   ├── templates/
│   │   └── admin/
│   └── static/
├── config.py
├── requirements.txt
└── run.py
```

#### Required Dependencies
```
Flask==2.0.1
Flask-SQLAlchemy==2.5.1
Flask-Login==0.5.0
cryptography==3.4.7
pyOpenSSL==20.0.1
requests==2.26.0
```

#### Acceptance Criteria
- [ ] Flask application structure implemented
- [ ] SQLAlchemy database integration
- [ ] User login system with Flask-Login
- [ ] Secure URL shortener utility
- [ ] MDM handler utilities
- [ ] Admin templates
- [ ] Cryptography for secure operations

---

### Issue #4: Set up Copilot Instructions

**Status**: Open (In Progress via PR #5)  
**Assignee**: Copilot

#### Requirements
- Configure instructions for this repository per [Best Practices for Copilot Coding Agent](https://gh.io/copilot-coding-agent-tips)

#### Acceptance Criteria
- [ ] `.github/copilot-instructions.md` created
- [ ] Project context documented
- [ ] Technical stack described
- [ ] Code style guidelines included
- [ ] Content guidelines for memorial site
- [ ] Testing/validation procedures documented

---

## Integration Notes

### GitHub Mobile ↔ Web Interface Synchronization

To ensure consistency across platforms:

1. **Issues**: All work items are tracked as GitHub Issues
2. **Pull Requests**: All code changes go through PRs
3. **Labels**: Use consistent labels (enhancement, documentation, etc.)
4. **Milestones**: Use GitHub Milestones for sprint/phase tracking
5. **Project Boards**: Consider using GitHub Projects for Kanban-style tracking

### Copilot Integration

The Copilot coding agent can:
- Work on issues assigned to it
- Create and update pull requests
- Follow instructions in `.github/copilot-instructions.md`
- Provide consistent code style and patterns

---

## Technical Stack Summary

| Layer | Technology | Purpose |
|-------|------------|---------|
| Frontend | HTML5, CSS3 | Memorial website |
| Frontend Fonts | Cormorant Garamond | Elegant typography |
| Backend (Planned) | Flask/FastAPI | Web server |
| Database (Planned) | SQLAlchemy | ORM/Data persistence |
| Auth (Planned) | Flask-Login | User authentication |
| Security (Planned) | cryptography, pyOpenSSL | Encryption, HTTPS |

---

## Next Steps

1. **Merge PR #5** to complete Copilot setup
2. **Begin Issue #1** - Set up Python web server foundation
3. **Begin Issue #2** - Implement MDM server components
4. **Enhance memorial site** - Add more photos, content as available

---

*Last Updated: November 27, 2025*
