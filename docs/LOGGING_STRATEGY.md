# Logging and Reporting Strategy

## Steganography and IP Capture Tool - V1

**Version:** 1.0.0  
**Status:** Draft  
**Last Updated:** November 2025

---

## Table of Contents

1. [Overview](#overview)
2. [Logging Philosophy](#logging-philosophy)
3. [Log Levels](#log-levels)
4. [Log Format Standards](#log-format-standards)
5. [Service-Specific Logging](#service-specific-logging)
6. [Centralized Logging Architecture](#centralized-logging-architecture)
7. [Error Tracking and Alerting](#error-tracking-and-alerting)
8. [Reporting Strategy](#reporting-strategy)
9. [Privacy and Compliance](#privacy-and-compliance)
10. [Implementation Guidelines](#implementation-guidelines)

---

## Overview

This document defines the logging and reporting strategy for the steganography and IP capture tool project. Proper logging is essential for:

- **Debugging**: Finding and fixing issues quickly
- **Monitoring**: Understanding system health and performance
- **Auditing**: Tracking who did what and when
- **Analytics**: Generating insights from usage patterns

Our approach balances detailed information capture with privacy protection and system performance.

---

## Logging Philosophy

### Core Principles

1. **Log with Purpose**
   - Every log entry should serve a specific purpose
   - Avoid logging for the sake of logging
   - Focus on actionable information

2. **Consistency Across Services**
   - Same format and structure in all services
   - Unified log levels and meanings
   - Centralized configuration

3. **Privacy by Default**
   - Sensitive data is never logged in plain text
   - IP addresses anonymized unless explicitly needed
   - PII (Personal Identifiable Information) redacted

4. **Performance Awareness**
   - Async logging for high-volume operations
   - Sampling for extremely frequent events
   - Buffer and batch log writes

---

## Log Levels

### Standard Log Levels

| Level | Code | When to Use | Example |
|-------|------|-------------|---------|
| **TRACE** | 5 | Very detailed debugging (disabled in production) | Function entry/exit, variable values |
| **DEBUG** | 10 | Detailed information for debugging | Request processing steps |
| **INFO** | 20 | Normal operations, significant events | Service started, job completed |
| **WARNING** | 30 | Something unexpected but recoverable | Retry attempt, deprecated feature used |
| **ERROR** | 40 | Error that prevents a specific operation | Failed to process image, API call failed |
| **CRITICAL** | 50 | System-wide failure | Database down, out of memory |

### Level Usage by Environment

| Environment | Default Level | Available Levels |
|-------------|--------------|------------------|
| Development | DEBUG | All (TRACE-CRITICAL) |
| Staging | INFO | DEBUG-CRITICAL |
| Production | INFO | INFO-CRITICAL |

### Level Guidelines

```python
# TRACE - Very detailed, only for deep debugging
logger.trace("Entering encode_message() with image_size=%d", image_size)

# DEBUG - Helpful for development and troubleshooting
logger.debug("Processing image format: %s, dimensions: %dx%d", format, width, height)

# INFO - Normal operations that are noteworthy
logger.info("Successfully encoded message into image, job_id=%s", job_id)

# WARNING - Unusual situations that don't prevent operation
logger.warning("Retry attempt %d for service %s", attempt, service_name)

# ERROR - Specific operation failures
logger.error("Failed to decode image: %s", error_message, exc_info=True)

# CRITICAL - System-wide issues requiring immediate attention
logger.critical("Database connection pool exhausted, shutting down")
```

---

## Log Format Standards

### Structured Logging Format

All logs follow a JSON structure for easy parsing and analysis:

```json
{
  "timestamp": "2025-11-27T12:00:00.123Z",
  "level": "INFO",
  "service": "steganography-engine",
  "version": "1.0.0",
  "environment": "production",
  "host": "steg-engine-01",
  "trace_id": "abc123def456",
  "span_id": "span789",
  "message": "Image encoding completed successfully",
  "context": {
    "job_id": "job-12345",
    "image_format": "PNG",
    "processing_time_ms": 145
  },
  "extra": {}
}
```

### Field Definitions

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `timestamp` | ISO 8601 string | Yes | When the event occurred |
| `level` | string | Yes | Log level (DEBUG, INFO, etc.) |
| `service` | string | Yes | Name of the service |
| `version` | string | Yes | Service version |
| `environment` | string | Yes | Environment (dev, staging, prod) |
| `host` | string | Yes | Server/container hostname |
| `trace_id` | string | No | Distributed tracing ID |
| `span_id` | string | No | Span ID for tracing |
| `message` | string | Yes | Human-readable message |
| `context` | object | No | Event-specific data |
| `extra` | object | No | Additional metadata |

### Human-Readable Format (Development)

For local development, a more readable format is available:

```
2025-11-27 12:00:00.123 | INFO     | steganography-engine | Image encoding completed | job_id=job-12345 | 145ms
```

---

## Service-Specific Logging

### Steganography Engine

**Key Events to Log:**

| Event | Level | Context Fields |
|-------|-------|----------------|
| Encode request received | INFO | job_id, format, image_size |
| Encode processing started | DEBUG | job_id, algorithm |
| Encode completed | INFO | job_id, processing_time_ms |
| Decode request received | INFO | job_id, image_size |
| Decode completed | INFO | job_id, message_length, processing_time_ms |
| Image validation failed | WARNING | job_id, reason |
| Encoding/Decoding error | ERROR | job_id, error_type, error_message |

**Example Log Entries:**

```python
# Request received
logger.info(
    "Encode request received",
    extra={
        "context": {
            "job_id": job_id,
            "format": "PNG",
            "image_size_bytes": file_size
        }
    }
)

# Processing error
logger.error(
    "Failed to encode message into image",
    extra={
        "context": {
            "job_id": job_id,
            "error_type": "UnsupportedFormat",
            "error_message": str(e)
        }
    },
    exc_info=True
)
```

---

### IP Capture Service

**Key Events to Log:**

| Event | Level | Context Fields |
|-------|-------|----------------|
| IP captured | INFO | request_id, ip_hash (anonymized) |
| Geo-lookup performed | DEBUG | request_id, country, region |
| Analytics query | INFO | query_type, time_range |
| Export requested | INFO | format, record_count |
| Rate limit exceeded | WARNING | client_id, limit |
| Database write failed | ERROR | operation, error_message |

**Privacy-Aware Logging:**

```python
def anonymize_ip(ip: str) -> str:
    """Anonymize IP for logging by zeroing last octet."""
    parts = ip.split('.')
    parts[-1] = '0'
    return '.'.join(parts)

# Log with anonymized IP
logger.info(
    "IP captured for analytics",
    extra={
        "context": {
            "request_id": request_id,
            "ip_hash": hash_ip(ip_address),
            "ip_anonymized": anonymize_ip(ip_address),
            "country": geo_data.country
        }
    }
)
```

---

### Orchestration Service

**Key Events to Log:**

| Event | Level | Context Fields |
|-------|-------|----------------|
| Workflow created | INFO | workflow_id, type |
| Workflow step started | DEBUG | workflow_id, step_name |
| Workflow completed | INFO | workflow_id, total_time_ms |
| Service call initiated | DEBUG | workflow_id, target_service |
| Service call succeeded | DEBUG | workflow_id, response_time_ms |
| Service call failed | WARNING | workflow_id, service, error |
| Circuit breaker opened | WARNING | service_name, failure_count |
| Background job queued | INFO | job_id, job_type |
| Background job completed | INFO | job_id, execution_time_ms |

---

## Centralized Logging Architecture

### Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                           Application Logs                           │
└─────────────────────────────────────────────────────────────────────┘
         │                      │                      │
         ▼                      ▼                      ▼
┌─────────────────┐   ┌─────────────────┐   ┌─────────────────┐
│  Steganography  │   │   IP Capture    │   │  Orchestration  │
│     Engine      │   │    Service      │   │    Service      │
└─────────────────┘   └─────────────────┘   └─────────────────┘
         │                      │                      │
         └──────────────────────┼──────────────────────┘
                                │
                                ▼
                    ┌─────────────────────────┐
                    │      Log Collector      │
                    │     (Fluentd/Logstash)  │
                    └─────────────────────────┘
                                │
                    ┌───────────┼───────────┐
                    ▼           ▼           ▼
            ┌─────────────┐ ┌─────────┐ ┌─────────┐
            │ Elasticsearch│ │ S3/GCS  │ │ Metrics │
            │   (Search)   │ │ (Archive)│ │(Prometheus)|
            └─────────────┘ └─────────┘ └─────────┘
                    │
                    ▼
            ┌─────────────────┐
            │    Kibana /     │
            │   Grafana UI    │
            └─────────────────┘
```

### Log Collection Configuration

**Fluentd Configuration Example:**

```yaml
# fluentd.conf
<source>
  @type forward
  port 24224
  bind 0.0.0.0
</source>

<filter **>
  @type parser
  key_name log
  reserve_data true
  <parse>
    @type json
  </parse>
</filter>

<match **>
  @type elasticsearch
  host elasticsearch
  port 9200
  logstash_format true
  logstash_prefix steg-tool
  <buffer>
    @type file
    path /var/log/fluentd-buffers/
    flush_interval 5s
    chunk_limit_size 5MB
    retry_max_interval 30
    retry_forever true
  </buffer>
</match>
```

### Log Retention Policy

| Log Type | Hot Storage | Warm Storage | Cold Storage | Total Retention |
|----------|-------------|--------------|--------------|-----------------|
| ERROR/CRITICAL | 30 days | 90 days | 1 year | 1+ years |
| WARNING | 14 days | 60 days | 6 months | 9 months |
| INFO | 7 days | 30 days | 90 days | 4 months |
| DEBUG | 3 days | - | - | 3 days |

---

## Error Tracking and Alerting

### Error Classification

| Category | Description | Response Time | Alert Channel |
|----------|-------------|---------------|---------------|
| P0 - Critical | System down, data loss | Immediate | PagerDuty + Slack |
| P1 - High | Major feature broken | < 1 hour | Slack + Email |
| P2 - Medium | Minor feature issue | < 4 hours | Slack |
| P3 - Low | Cosmetic/minor issues | Next sprint | None |

### Alert Rules

```yaml
# alertmanager-rules.yml
groups:
  - name: service-alerts
    rules:
      - alert: HighErrorRate
        expr: rate(log_entries_total{level="ERROR"}[5m]) > 10
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "High error rate detected"
          description: "Error rate is {{ $value }} errors per second"
      
      - alert: ServiceDown
        expr: up{job=~"steg.*"} == 0
        for: 1m
        labels:
          severity: critical
        annotations:
          summary: "Service {{ $labels.instance }} is down"
      
      - alert: SlowResponseTime
        expr: http_request_duration_seconds{quantile="0.99"} > 2
        for: 10m
        labels:
          severity: warning
        annotations:
          summary: "Slow response times detected"
```

### Error Context Collection

When errors occur, collect:

```python
def log_error_with_context(error, request=None):
    """Log error with full context for debugging."""
    context = {
        "error_type": type(error).__name__,
        "error_message": str(error),
        "stack_trace": traceback.format_exc(),
    }
    
    if request:
        context.update({
            "request_id": request.state.request_id,
            "method": request.method,
            "path": request.url.path,
            "user_agent": request.headers.get("user-agent", "unknown"),
        })
    
    logger.error(
        f"Error occurred: {error}",
        extra={"context": context},
        exc_info=True
    )
```

---

## Reporting Strategy

### Report Types

#### 1. Operational Reports (Real-time)

**Purpose:** Monitor system health and performance

| Metric | Collection Interval | Visualization |
|--------|---------------------|---------------|
| Request count | 1 minute | Line chart |
| Error rate | 1 minute | Line chart with threshold |
| Response time (p50, p95, p99) | 1 minute | Percentile chart |
| Active connections | 30 seconds | Gauge |
| Queue depth | 30 seconds | Gauge |

#### 2. Usage Reports (Daily/Weekly)

**Purpose:** Understand usage patterns and trends

| Report | Frequency | Contents |
|--------|-----------|----------|
| Daily Summary | Daily | Total requests, errors, unique users |
| Weekly Trends | Weekly | Usage trends, top operations, growth |
| Monthly Capacity | Monthly | Resource usage, scaling needs |

#### 3. Audit Reports (On-demand)

**Purpose:** Security and compliance

| Report | Trigger | Contents |
|--------|---------|----------|
| Access Log | On-demand | Who accessed what and when |
| Change Log | On-demand | Configuration and data changes |
| Security Events | Real-time | Suspicious activities, failed auth |

### Dashboard Structure

```
┌─────────────────────────────────────────────────────────────────────┐
│                        OPERATIONS DASHBOARD                          │
├─────────────────────────────────────────────────────────────────────┤
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌──────────┐│
│  │ Requests/min │  │  Error Rate  │  │ Avg Latency  │  │ Uptime   ││
│  │    1,234     │  │    0.1%      │  │    45ms      │  │  99.9%   ││
│  └──────────────┘  └──────────────┘  └──────────────┘  └──────────┘│
├─────────────────────────────────────────────────────────────────────┤
│  ┌─────────────────────────────────────────────────────────────────┐│
│  │                    Request Rate (24 hours)                       ││
│  │  ▁▂▃▄▅▆▇█▇▆▅▄▃▂▁▁▂▃▄▅▆▇█▇▆▅▄▃▂▁                                 ││
│  └─────────────────────────────────────────────────────────────────┘│
├─────────────────────────────────────────────────────────────────────┤
│  ┌──────────────────────────┐  ┌──────────────────────────────────┐│
│  │   Service Health         │  │     Recent Errors                ││
│  │  ✓ Steganography: OK     │  │  • [12:01] Encode failed - PNG   ││
│  │  ✓ IP Capture: OK        │  │  • [11:58] DB timeout            ││
│  │  ✓ Orchestration: OK     │  │  • [11:45] Rate limit exceeded   ││
│  │  ✓ Database: OK          │  │                                  ││
│  │  ✓ Redis: OK             │  │                                  ││
│  └──────────────────────────┘  └──────────────────────────────────┘│
└─────────────────────────────────────────────────────────────────────┘
```

---

## Privacy and Compliance

### Data Handling Rules

| Data Type | Logging Allowed | Storage | Anonymization |
|-----------|-----------------|---------|---------------|
| IP Address | With anonymization | 30 days | Last octet zeroed |
| Email | No | - | - |
| User Agent | Yes | 90 days | None needed |
| Request Body | DEBUG only | 3 days | Redact sensitive fields |
| Credentials | Never | - | - |
| Hidden Messages | Never | - | - |

### Anonymization Functions

```python
import hashlib
import re

def anonymize_ip(ip: str) -> str:
    """Zero the last octet of IPv4 address."""
    if ':' in ip:  # IPv6
        parts = ip.split(':')
        return ':'.join(parts[:4] + ['0000'] * 4)
    else:  # IPv4
        parts = ip.split('.')
        parts[-1] = '0'
        return '.'.join(parts)

def hash_identifier(value: str) -> str:
    """Create consistent hash for tracking without exposing value."""
    return hashlib.sha256(value.encode()).hexdigest()[:16]

def redact_sensitive(data: dict) -> dict:
    """Redact sensitive fields from log data."""
    sensitive_patterns = ['password', 'secret', 'token', 'key', 'auth']
    redacted = data.copy()
    
    for key in redacted:
        if any(pattern in key.lower() for pattern in sensitive_patterns):
            redacted[key] = '[REDACTED]'
    
    return redacted
```

### GDPR Compliance

1. **Right to Access**: Logs can be queried by user identifier
2. **Right to Erasure**: Automated purge based on retention policy
3. **Data Minimization**: Only necessary data is logged
4. **Purpose Limitation**: Logs used only for operations and debugging

---

## Implementation Guidelines

### Python Logging Setup

```python
# config/logging.py
import logging
import json
import sys
from datetime import datetime

class JSONFormatter(logging.Formatter):
    """Custom JSON formatter for structured logging."""
    
    def format(self, record):
        log_entry = {
            "timestamp": datetime.utcnow().isoformat() + "Z",
            "level": record.levelname,
            "service": getattr(record, 'service', 'unknown'),
            "version": getattr(record, 'version', '1.0.0'),
            "environment": getattr(record, 'environment', 'development'),
            "host": getattr(record, 'host', 'localhost'),
            "message": record.getMessage(),
            "context": getattr(record, 'context', {}),
        }
        
        if record.exc_info:
            log_entry["exception"] = self.formatException(record.exc_info)
        
        return json.dumps(log_entry)

def setup_logging(service_name: str, log_level: str = "INFO"):
    """Configure logging for a service."""
    logger = logging.getLogger(service_name)
    logger.setLevel(getattr(logging, log_level.upper()))
    
    handler = logging.StreamHandler(sys.stdout)
    handler.setFormatter(JSONFormatter())
    logger.addHandler(handler)
    
    return logger

# Usage
logger = setup_logging("steganography-engine", "INFO")
```

### FastAPI Integration

```python
# middleware/logging.py
import time
import uuid
from fastapi import Request
from starlette.middleware.base import BaseHTTPMiddleware

class LoggingMiddleware(BaseHTTPMiddleware):
    """Middleware to log all HTTP requests."""
    
    async def dispatch(self, request: Request, call_next):
        request_id = str(uuid.uuid4())
        start_time = time.time()
        
        # Add request ID to request state
        request.state.request_id = request_id
        
        # Log request
        logger.info(
            "Request received",
            extra={
                "context": {
                    "request_id": request_id,
                    "method": request.method,
                    "path": request.url.path,
                    "client_ip": anonymize_ip(request.client.host),
                }
            }
        )
        
        response = await call_next(request)
        
        # Log response
        processing_time = (time.time() - start_time) * 1000
        logger.info(
            "Request completed",
            extra={
                "context": {
                    "request_id": request_id,
                    "status_code": response.status_code,
                    "processing_time_ms": round(processing_time, 2),
                }
            }
        )
        
        response.headers["X-Request-ID"] = request_id
        return response
```

### Testing Logging

```python
# tests/test_logging.py
import logging
import pytest
from io import StringIO

def test_logs_request_id():
    """Verify that request ID is included in logs."""
    log_capture = StringIO()
    handler = logging.StreamHandler(log_capture)
    logger = logging.getLogger("test")
    logger.addHandler(handler)
    
    logger.info("Test message", extra={"context": {"request_id": "test-123"}})
    
    log_output = log_capture.getvalue()
    assert "test-123" in log_output

def test_sensitive_data_redaction():
    """Verify sensitive data is redacted."""
    from config.logging import redact_sensitive
    
    data = {"username": "john", "password": "secret123"}
    redacted = redact_sensitive(data)
    
    assert redacted["username"] == "john"
    assert redacted["password"] == "[REDACTED]"
```

---

## Appendix

### Log Analysis Queries

**Find all errors in last hour:**
```
GET /steg-tool-*/_search
{
  "query": {
    "bool": {
      "must": [
        { "match": { "level": "ERROR" } },
        { "range": { "timestamp": { "gte": "now-1h" } } }
      ]
    }
  }
}
```

**Get request count by service:**
```
GET /steg-tool-*/_search
{
  "size": 0,
  "aggs": {
    "by_service": {
      "terms": { "field": "service.keyword" }
    }
  }
}
```

### References

- [Python Logging Documentation](https://docs.python.org/3/library/logging.html)
- [12-Factor App Logging](https://12factor.net/logs)
- [OWASP Logging Guide](https://owasp.org/www-project-logging-guide/)
- [ELK Stack Documentation](https://www.elastic.co/guide/index.html)

---

*Document maintained by the development team. For questions or updates, please open an issue in the repository.*
