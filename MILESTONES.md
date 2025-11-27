# Alva-Memorial Project Milestones

> This document outlines the project roadmap and milestones to help track progress and coordinate work across GitHub Mobile, Web Interface, and Copilot.

---

## Milestone Overview

| Milestone | Description | Status | Target |
|-----------|-------------|--------|--------|
| M0 | Project Setup & Documentation | 🔄 In Progress | Q4 2025 |
| M1 | Memorial Website Enhancement | 📋 Planned | Q4 2025 |
| M2 | Python Web Server Foundation | 📋 Planned | Q1 2026 |
| M3 | MDM Server Implementation | 📋 Planned | Q1 2026 |
| M4 | Security & Privacy Hardening | 📋 Planned | Q2 2026 |

---

## M0: Project Setup & Documentation *(Current)*

**Goal**: Establish project infrastructure and documentation

### Tasks
- [x] Create memorial website (index.html)
- [x] Set up GitHub repository
- [x] Enable GitHub Pages
- [ ] Complete Copilot instructions (PR #5)
- [x] Document project requirements (PROJECT_REQUIREMENTS.md)
- [x] Create project milestones (MILESTONES.md)

### Deliverables
- Repository with clear structure
- Documentation for contributors
- Copilot integration configured

---

## M1: Memorial Website Enhancement

**Goal**: Enhance the memorial website with additional content and features

### Tasks
- [ ] Add additional family photos to gallery
- [ ] Create mobile-optimized styles
- [ ] Add accessibility improvements (ARIA labels, alt text)
- [ ] Implement print-friendly stylesheet
- [ ] Add favicon and meta tags for social sharing
- [ ] Test across browsers (Chrome, Firefox, Safari, Edge)

### Acceptance Criteria
- [ ] Lighthouse accessibility score > 90
- [ ] Mobile-responsive design verified
- [ ] Print layout tested
- [ ] All images have alt text

---

## M2: Python Web Server Foundation

**Goal**: Establish the foundation for the Python web server (Issue #1)

### Phase 2A: Basic Server Setup
- [ ] Choose framework (Flask vs FastAPI)
- [ ] Create project structure
- [ ] Set up development environment
- [ ] Implement basic routing
- [ ] Add configuration management

### Phase 2B: Security Implementation
- [ ] Add HTTPS/TLS support
- [ ] Implement input validation
- [ ] Add authentication framework
- [ ] Create security headers middleware
- [ ] Document security practices

### Phase 2C: Localization
- [ ] Add i18n/l10n support
- [ ] Create language resource files
- [ ] Implement locale detection
- [ ] Test with multiple languages

### Phase 2D: Privacy Features
- [ ] Implement privacy-focused logging
- [ ] Add data retention policies
- [ ] Document privacy practices
- [ ] (Optional) Tor integration research

### Dependencies
```
Flask>=2.0.0 or FastAPI>=0.68.0
python-i18n>=0.3.9
cryptography>=3.4.0
python-dotenv>=0.19.0
```

---

## M3: MDM Server Implementation

**Goal**: Implement MDM Web Server with URL Shortener (Issue #2)

### Phase 3A: Core Structure
- [ ] Create `mdm_server/` directory structure
- [ ] Implement Flask application factory
- [ ] Set up SQLAlchemy models
- [ ] Create database migrations

### Phase 3B: URL Shortener
- [ ] Design URL shortening algorithm
- [ ] Implement `url_shortener.py` utility
- [ ] Create API endpoints for short URLs
- [ ] Add URL validation and sanitization

### Phase 3C: MDM Handler
- [ ] Research MDM protocol requirements
- [ ] Implement `mdm_handler.py`
- [ ] Create device enrollment flow
- [ ] Add policy management

### Phase 3D: Admin Interface
- [ ] Create admin templates
- [ ] Implement user management
- [ ] Add analytics dashboard
- [ ] Create URL management interface

### Dependencies
```
Flask==2.0.1
Flask-SQLAlchemy==2.5.1
Flask-Login==0.5.0
cryptography==3.4.7
pyOpenSSL==20.0.1
requests==2.26.0
```

---

## M4: Security & Privacy Hardening

**Goal**: Comprehensive security audit and hardening

### Tasks
- [ ] Conduct security audit
- [ ] Implement security recommendations
- [ ] Add automated security scanning (CodeQL)
- [ ] Create security documentation
- [ ] Set up dependency vulnerability monitoring
- [ ] Implement rate limiting
- [ ] Add CORS configuration
- [ ] Create incident response plan

### Security Checklist
- [ ] All dependencies updated to latest secure versions
- [ ] No secrets in source code
- [ ] HTTPS enforced everywhere
- [ ] SQL injection prevention verified
- [ ] XSS prevention verified
- [ ] CSRF protection enabled
- [ ] Security headers configured

---

## Work Coordination

### GitHub Mobile Tips
- Star/watch the repository for notifications
- Use issue filters to track assigned work
- Review PR diffs in the mobile app
- Comment on issues for quick updates

### Web Interface Tips
- Use Projects board for visual task tracking
- Assign issues to specific milestones
- Use PR draft mode for work in progress
- Link commits to issues with "Fixes #X"

### Copilot Agent Tips
- Assign issues to Copilot for automated assistance
- Keep `.github/copilot-instructions.md` updated
- Review Copilot PRs thoroughly before merging
- Use issue templates for consistent requests

---

## Progress Tracking

### How to Update
1. Check off completed items in this document
2. Update status emoji in milestone table
3. Add completion dates to milestone headers
4. Create new milestones as needed

### Status Legend
- 📋 Planned - Not yet started
- 🔄 In Progress - Actively being worked on
- ✅ Complete - Finished and verified
- ⏸️ On Hold - Paused for external reasons
- ❌ Blocked - Cannot proceed due to dependency

---

## Related Links

- [Project Requirements](./PROJECT_REQUIREMENTS.md)
- [Issue #1 - Python Web Server](https://github.com/RizzilientLilLeo/Alva-Memorial/issues/1)
- [Issue #2 - MDM Server](https://github.com/RizzilientLilLeo/Alva-Memorial/issues/2)
- [Issue #4 - Copilot Setup](https://github.com/RizzilientLilLeo/Alva-Memorial/issues/4)
- [PR #5 - Copilot Instructions](https://github.com/RizzilientLilLeo/Alva-Memorial/pull/5)

---

*Last Updated: November 27, 2025*
