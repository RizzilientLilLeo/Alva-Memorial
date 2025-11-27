# Technical Specification Document

## Steganography and IP Capture Tool - V1

**Version:** 1.0.0  
**Status:** Draft  
**Last Updated:** November 2025

---

## Table of Contents

1. [Overview](#overview)
2. [Project Goals](#project-goals)
3. [System Architecture](#system-architecture)
4. [Microservices Description](#microservices-description)
5. [Data Flow](#data-flow)
6. [Technical Requirements](#technical-requirements)
7. [API Specifications](#api-specifications)
8. [Security Considerations](#security-considerations)
9. [Testing Strategy](#testing-strategy)
10. [Deployment Strategy](#deployment-strategy)
11. [Future Enhancements](#future-enhancements)

---

## Overview

This document outlines the technical specification for a steganography and IP capture tool. The system is designed using a microservices architecture to provide modular, scalable, and maintainable functionality for:

- **Steganography**: Hiding data within image files (PNG, JPEG, SVG)
- **IP Capture**: Recording visitor/request IP addresses for analytics
- **Orchestration**: Coordinating workflows between services

The tool is built with Python 3.x and follows modern software development practices including containerization, CI/CD, and comprehensive testing.

---

## Project Goals

### Primary Objectives

1. **Create a modular steganography engine** capable of encoding and decoding hidden messages in various image formats
2. **Develop an IP capture service** for logging and analyzing network requests
3. **Build an orchestration layer** to manage workflows and service communication
4. **Ensure privacy and security** in all data handling operations
5. **Provide community-friendly documentation** for easy onboarding

### Success Criteria

- Successfully encode/decode messages in PNG, JPEG, and SVG formats
- Capture and log IP addresses with configurable privacy settings
- Process requests with response times under 500ms for standard operations
- Achieve 80%+ test coverage across all services
- Complete documentation for all public APIs

---

## System Architecture

### High-Level Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                         Client Applications                          │
│                    (Web UI / CLI / API Consumers)                   │
└─────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────┐
│                       API Gateway / Load Balancer                    │
└─────────────────────────────────────────────────────────────────────┘
                                    │
                    ┌───────────────┼───────────────┐
                    ▼               ▼               ▼
┌───────────────────────┐ ┌─────────────────┐ ┌─────────────────────┐
│   Orchestration       │ │  Steganography  │ │    IP Capture       │
│   Service             │ │  Engine         │ │    Service          │
│                       │ │                 │ │                     │
│ • Workflow Management │ │ • Encode/Decode │ │ • IP Logging        │
│ • Service Routing     │ │ • Format Convert│ │ • Geo-location      │
│ • Job Scheduling      │ │ • Image Validate│ │ • Analytics         │
└───────────────────────┘ └─────────────────┘ └─────────────────────┘
           │                       │                     │
           └───────────────────────┼─────────────────────┘
                                   ▼
                    ┌─────────────────────────────┐
                    │       Message Queue         │
                    │    (Redis / RabbitMQ)       │
                    └─────────────────────────────┘
                                   │
                                   ▼
                    ┌─────────────────────────────┐
                    │       Data Storage          │
                    │  (PostgreSQL / File Store)  │
                    └─────────────────────────────┘
```

### Component Communication

| Source Service | Target Service | Protocol | Description |
|---------------|----------------|----------|-------------|
| Client | API Gateway | HTTPS | External API requests |
| API Gateway | All Services | HTTP/gRPC | Internal routing |
| Orchestration | Steganography | HTTP | Image processing requests |
| Orchestration | IP Capture | HTTP | Logging requests |
| All Services | Message Queue | AMQP/Redis | Async task handling |
| All Services | Data Storage | TCP | Data persistence |

---

## Microservices Description

### 1. Steganography Engine

**Purpose:** Encode and decode hidden data within image files.

**Responsibilities:**
- Accept image files in PNG, JPEG, and SVG formats
- Encode text or binary data into images using LSB (Least Significant Bit) technique
- Decode hidden data from steganographic images
- Validate image integrity before and after operations
- Support batch processing for multiple files

**Technology Stack:**
- Python 3.10+
- PIL/Pillow (Image manipulation)
- Stegano (Steganography algorithms)
- svgwrite (SVG generation and modification)
- NumPy (Numerical operations for pixel manipulation)

**API Endpoints:**
```
POST /api/v1/steg/encode    - Encode data into image
POST /api/v1/steg/decode    - Decode data from image
POST /api/v1/steg/validate  - Validate image for steganography
GET  /api/v1/steg/formats   - List supported formats
GET  /api/v1/steg/health    - Service health check
```

**Service Placeholder Structure:**
```python
# services/steganography_engine/
├── __init__.py
├── app.py                  # FastAPI application entry
├── config.py               # Service configuration
├── encoders/
│   ├── __init__.py
│   ├── base.py            # Abstract encoder class
│   ├── png_encoder.py     # PNG-specific encoding
│   ├── jpeg_encoder.py    # JPEG-specific encoding
│   └── svg_encoder.py     # SVG-specific encoding
├── decoders/
│   ├── __init__.py
│   ├── base.py            # Abstract decoder class
│   ├── png_decoder.py     # PNG-specific decoding
│   ├── jpeg_decoder.py    # JPEG-specific decoding
│   └── svg_decoder.py     # SVG-specific decoding
├── validators/
│   ├── __init__.py
│   └── image_validator.py # Image validation utilities
├── models/
│   ├── __init__.py
│   └── schemas.py         # Pydantic models
├── tests/
│   ├── __init__.py
│   ├── test_encoders.py
│   ├── test_decoders.py
│   └── test_validators.py
└── requirements.txt
```

---

### 2. IP Capture Service

**Purpose:** Capture, log, and analyze IP addresses from incoming requests.

**Responsibilities:**
- Extract IP addresses from HTTP headers (X-Forwarded-For, X-Real-IP)
- Log IP data with timestamps and request metadata
- Provide optional geo-location lookup
- Generate analytics reports
- Support privacy-compliant data handling (IP anonymization)

**Technology Stack:**
- Python 3.10+
- FastAPI (Web framework)
- GeoIP2 (Geo-location lookup - optional)
- SQLAlchemy (Database ORM)
- Redis (Caching and rate limiting)

**API Endpoints:**
```
POST /api/v1/ip/capture     - Capture and log IP
GET  /api/v1/ip/lookup/{ip} - Lookup IP details
GET  /api/v1/ip/analytics   - Get analytics dashboard data
GET  /api/v1/ip/export      - Export logs (CSV/JSON)
GET  /api/v1/ip/health      - Service health check
```

**Service Placeholder Structure:**
```python
# services/ip_capture_service/
├── __init__.py
├── app.py                  # FastAPI application entry
├── config.py               # Service configuration
├── capture/
│   ├── __init__.py
│   ├── extractor.py       # IP extraction utilities
│   ├── logger.py          # IP logging functionality
│   └── anonymizer.py      # IP anonymization for privacy
├── analytics/
│   ├── __init__.py
│   ├── aggregator.py      # Data aggregation
│   └── reports.py         # Report generation
├── geo/
│   ├── __init__.py
│   └── locator.py         # Geo-location services
├── models/
│   ├── __init__.py
│   ├── database.py        # Database models
│   └── schemas.py         # Pydantic models
├── tests/
│   ├── __init__.py
│   ├── test_capture.py
│   ├── test_analytics.py
│   └── test_geo.py
└── requirements.txt
```

---

### 3. Orchestration Service

**Purpose:** Coordinate workflows between steganography and IP capture services.

**Responsibilities:**
- Manage multi-step workflows (e.g., capture IP → encode into image)
- Handle service discovery and health monitoring
- Implement retry logic and circuit breaker patterns
- Schedule and manage background jobs
- Provide unified API for complex operations

**Technology Stack:**
- Python 3.10+
- FastAPI (Web framework)
- Celery (Task queue)
- Redis (Message broker)
- httpx (Async HTTP client)

**API Endpoints:**
```
POST /api/v1/workflow/create   - Create new workflow
GET  /api/v1/workflow/{id}     - Get workflow status
POST /api/v1/workflow/{id}/run - Execute workflow
GET  /api/v1/services/health   - Check all services health
GET  /api/v1/jobs              - List background jobs
GET  /api/v1/health            - Service health check
```

**Service Placeholder Structure:**
```python
# services/orchestration_service/
├── __init__.py
├── app.py                  # FastAPI application entry
├── config.py               # Service configuration
├── workflows/
│   ├── __init__.py
│   ├── base.py            # Base workflow class
│   ├── steg_ip_workflow.py # Steganography + IP workflow
│   └── registry.py        # Workflow registry
├── clients/
│   ├── __init__.py
│   ├── steg_client.py     # Steganography service client
│   └── ip_client.py       # IP capture service client
├── scheduler/
│   ├── __init__.py
│   ├── tasks.py           # Celery tasks
│   └── jobs.py            # Job management
├── monitoring/
│   ├── __init__.py
│   ├── health_checker.py  # Service health monitoring
│   └── circuit_breaker.py # Circuit breaker implementation
├── models/
│   ├── __init__.py
│   └── schemas.py         # Pydantic models
├── tests/
│   ├── __init__.py
│   ├── test_workflows.py
│   ├── test_clients.py
│   └── test_scheduler.py
└── requirements.txt
```

---

## Data Flow

### Encoding Workflow

```
┌──────────┐    ┌─────────────────┐    ┌────────────────────┐
│  Client  │───▶│  Orchestration  │───▶│   IP Capture       │
│          │    │  Service        │    │   Service          │
└──────────┘    └─────────────────┘    └────────────────────┘
                        │                        │
                        │                        │ IP Logged
                        ▼                        ▼
                ┌─────────────────┐    ┌────────────────────┐
                │  Steganography  │    │   Database         │
                │  Engine         │    │                    │
                └─────────────────┘    └────────────────────┘
                        │
                        │ Encoded Image
                        ▼
                ┌─────────────────┐
                │  File Storage   │
                └─────────────────┘
```

### Data Flow Steps

1. **Client Request**
   - Client sends request with image and data to encode
   - Request includes authentication token

2. **Orchestration Processing**
   - Validates request and authentication
   - Extracts client IP and forwards to IP Capture Service
   - Queues steganography job

3. **IP Capture**
   - Logs IP address with timestamp
   - Optionally performs geo-location lookup
   - Returns capture confirmation

4. **Steganography Processing**
   - Validates input image
   - Encodes data using appropriate algorithm
   - Saves encoded image to file storage

5. **Response**
   - Returns encoded image URL or file
   - Includes job metadata and status

---

## Technical Requirements

### Python Environment

```
Python Version: 3.10+
Virtual Environment: venv or conda recommended
Package Manager: pip or poetry
```

### Core Dependencies

| Package | Version | Purpose |
|---------|---------|---------|
| Pillow | >=10.0.0 | Image processing and manipulation |
| svgwrite | >=1.4.0 | SVG file creation and modification |
| stegano | >=0.11.0 | Steganography algorithms (alternative to PySteg) |
| FastAPI | >=0.100.0 | Web framework for APIs |
| uvicorn | >=0.23.0 | ASGI server |
| pydantic | >=2.0.0 | Data validation |
| SQLAlchemy | >=2.0.0 | Database ORM |
| Redis | >=5.0.0 | Caching and message broker |
| Celery | >=5.3.0 | Task queue |
| httpx | >=0.25.0 | Async HTTP client |
| pytest | >=7.4.0 | Testing framework |
| pytest-cov | >=4.1.0 | Test coverage |
| GeoIP2 | >=4.7.0 | Geo-location (optional) |
| NumPy | >=1.25.0 | Numerical operations |

### requirements.txt Template

```txt
# Core
python>=3.10

# Web Framework
fastapi>=0.100.0
uvicorn[standard]>=0.23.0
pydantic>=2.0.0

# Image Processing
Pillow>=10.0.0
svgwrite>=1.4.0
stegano>=0.11.0
numpy>=1.25.0

# Database
sqlalchemy>=2.0.0
alembic>=1.12.0
asyncpg>=0.28.0

# Caching & Messaging
redis>=5.0.0
celery>=5.3.0

# HTTP Client
httpx>=0.25.0

# Geo-location (optional)
geoip2>=4.7.0

# Testing
pytest>=7.4.0
pytest-cov>=4.1.0
pytest-asyncio>=0.21.0

# Development
black>=23.0.0
isort>=5.12.0
mypy>=1.5.0
flake8>=6.1.0
```

### System Requirements

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| CPU | 2 cores | 4+ cores |
| RAM | 4 GB | 8+ GB |
| Storage | 20 GB | 50+ GB |
| OS | Linux/macOS/Windows | Linux (Ubuntu 22.04+) |

### Infrastructure

```yaml
# docker-compose.yml structure
version: '3.8'
services:
  orchestration:
    build: ./services/orchestration_service
    ports:
      - "8000:8000"
    depends_on:
      - redis
      - postgres
  
  steganography:
    build: ./services/steganography_engine
    ports:
      - "8001:8001"
  
  ip-capture:
    build: ./services/ip_capture_service
    ports:
      - "8002:8002"
    depends_on:
      - postgres
  
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
  
  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: steg_tool
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

---

## API Specifications

### Authentication

All API endpoints (except health checks) require authentication via Bearer token:

```http
Authorization: Bearer <access_token>
```

### Standard Response Format

**Success Response:**
```json
{
  "success": true,
  "data": { ... },
  "meta": {
    "timestamp": "2025-11-27T12:00:00Z",
    "request_id": "uuid-v4",
    "version": "1.0.0"
  }
}
```

**Error Response:**
```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human readable error message",
    "details": { ... }
  },
  "meta": {
    "timestamp": "2025-11-27T12:00:00Z",
    "request_id": "uuid-v4",
    "version": "1.0.0"
  }
}
```

### Rate Limiting

| Endpoint Type | Limit | Window |
|--------------|-------|--------|
| Public | 100 requests | 1 minute |
| Authenticated | 1000 requests | 1 minute |
| Admin | Unlimited | - |

---

## Security Considerations

### Data Protection

1. **Encryption at Rest**
   - All stored data encrypted using AES-256
   - Database encryption enabled
   - File storage with server-side encryption

2. **Encryption in Transit**
   - TLS 1.3 for all communications
   - Certificate pinning for internal services

3. **IP Privacy**
   - Option to anonymize last octet of IPv4
   - Configurable data retention periods
   - GDPR-compliant data handling

### Input Validation

- All file uploads validated for type and size
- Maximum file size: 10MB (configurable)
- Allowed formats: PNG, JPEG, JPG, SVG
- Content-type verification
- Malware scanning (optional integration)

### Access Control

- Role-based access control (RBAC)
- API key rotation
- Audit logging for all operations
- IP whitelisting for admin endpoints

---

## Testing Strategy

### Test Pyramid

```
         ┌─────────────┐
         │   E2E       │  5%
         │   Tests     │
         └─────────────┘
        ┌───────────────┐
        │  Integration  │  20%
        │    Tests      │
        └───────────────┘
       ┌─────────────────┐
       │     Unit        │  75%
       │     Tests       │
       └─────────────────┘
```

### Unit Tests

- Test individual functions and methods
- Mock external dependencies
- Target: 80%+ code coverage
- Framework: pytest

```python
# Example test structure
def test_png_encoder_encodes_message():
    """Test PNG encoder successfully encodes message."""
    encoder = PNGEncoder()
    image = Image.new('RGB', (100, 100), color='white')
    message = "Secret message"
    
    result = encoder.encode(image, message)
    
    assert result is not None
    assert isinstance(result, Image.Image)
```

### Integration Tests

- Test service interactions
- Use test databases and mock services
- Validate API contracts
- Framework: pytest with httpx

```python
# Example integration test
async def test_encode_workflow():
    """Test full encoding workflow through orchestration."""
    async with AsyncClient(app=app) as client:
        response = await client.post(
            "/api/v1/workflow/create",
            json={"type": "encode", "data": "test"}
        )
        assert response.status_code == 201
```

### End-to-End Tests

- Test complete user workflows
- Use staging environment
- Validate system behavior
- Framework: pytest with Selenium (for UI) or httpx (for API)

### Test Coverage Requirements

| Service | Minimum Coverage |
|---------|-----------------|
| Steganography Engine | 85% |
| IP Capture Service | 80% |
| Orchestration Service | 75% |
| Overall | 80% |

### Continuous Integration

```yaml
# .github/workflows/test.yml
name: Tests
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.10'
      - name: Install dependencies
        run: pip install -r requirements.txt
      - name: Run tests
        run: pytest --cov=. --cov-report=xml
      - name: Upload coverage
        uses: codecov/codecov-action@v3
```

---

## Deployment Strategy

### Environments

| Environment | Purpose | Update Frequency |
|-------------|---------|------------------|
| Development | Local development | On save |
| Staging | Integration testing | On PR merge |
| Production | Live users | On release |

### Container Deployment

```dockerfile
# Dockerfile template
FROM python:3.10-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000

CMD ["uvicorn", "app:app", "--host", "0.0.0.0", "--port", "8000"]
```

### Health Checks

Each service exposes a `/health` endpoint returning:

```json
{
  "status": "healthy",
  "service": "steganography-engine",
  "version": "1.0.0",
  "uptime": 3600,
  "dependencies": {
    "redis": "healthy",
    "database": "healthy"
  }
}
```

---

## Future Enhancements

### V2 Roadmap

1. **Audio Steganography** - Support for hiding data in audio files
2. **Video Steganography** - Support for video file formats
3. **Advanced Encryption** - Additional encryption layers for hidden data
4. **Machine Learning** - Detection and prevention of steganalysis
5. **Web UI** - User-friendly web interface
6. **Mobile SDK** - iOS and Android client libraries

### Performance Improvements

1. GPU acceleration for image processing
2. Distributed processing for large batches
3. CDN integration for image delivery
4. Advanced caching strategies

---

## Appendix

### Glossary

| Term | Definition |
|------|------------|
| **Steganography** | The practice of hiding information within other data (like images) so that the presence of the hidden information is not evident |
| **LSB** | Least Significant Bit - a common technique for hiding data in images by modifying the least important bits of pixel values |
| **Microservices** | An architectural style where an application is built as a collection of small, independent services |
| **API Gateway** | A server that acts as an entry point for all client requests and routes them to appropriate services |
| **Circuit Breaker** | A design pattern that prevents a failure in one service from cascading to other services |

### References

- [Pillow Documentation](https://pillow.readthedocs.io/)
- [FastAPI Documentation](https://fastapi.tiangolo.com/)
- [Python Steganography Libraries](https://pypi.org/project/stegano/)
- [OWASP Security Guidelines](https://owasp.org/)

---

*Document maintained by the development team. For questions or updates, please open an issue in the repository.*
