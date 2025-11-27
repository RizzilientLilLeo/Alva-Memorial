# Steganography & IP Capture Tool

> A modular toolkit for embedding hidden data in images and capturing network request information.

[![Python Version](https://img.shields.io/badge/python-3.10%2B-blue.svg)](https://python.org)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![Build Status](https://img.shields.io/badge/build-passing-brightgreen.svg)]()
[![Coverage](https://img.shields.io/badge/coverage-80%25-yellowgreen.svg)]()

---

## Table of Contents

- [What is This?](#what-is-this)
- [Features](#features)
- [Quick Start](#quick-start)
- [Installation](#installation)
- [Usage](#usage)
- [Project Structure](#project-structure)
- [Configuration](#configuration)
- [API Reference](#api-reference)
- [Development](#development)
- [Testing](#testing)
- [Deployment](#deployment)
- [Contributing](#contributing)
- [FAQ](#faq)
- [License](#license)

---

## What is This?

This project provides tools for:

1. **Hiding Data in Images (Steganography)**: Embed secret messages or data within image files without visibly changing them
2. **Capturing IP Information**: Log and analyze visitor IP addresses from web requests
3. **Workflow Orchestration**: Combine these capabilities into automated workflows

**In Simple Terms**: Think of it like invisible ink for digital images, combined with a visitor logbook for your web services.

---

## Features

### Steganography Engine
- ✅ Encode text messages into PNG, JPEG, and SVG images
- ✅ Decode hidden messages from images
- ✅ Batch processing for multiple files
- ✅ Image validation before processing
- ✅ Support for binary data encoding

### IP Capture Service
- ✅ Automatic IP extraction from HTTP requests
- ✅ Privacy-compliant logging options
- ✅ Geo-location lookup (optional)
- ✅ Analytics and reporting
- ✅ Export to CSV/JSON

### Orchestration Service
- ✅ Multi-step workflow automation
- ✅ Background job processing
- ✅ Service health monitoring
- ✅ Retry logic with circuit breakers

---

## Quick Start

Get up and running in 5 minutes:

```bash
# Clone the repository
git clone https://github.com/your-username/steg-ip-tool.git
cd steg-ip-tool

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Run the development server
python -m uvicorn orchestration_service.app:app --reload
```

Visit `http://localhost:8000/docs` to see the interactive API documentation.

---

## Installation

### Prerequisites

- Python 3.10 or higher
- pip (Python package manager)
- Git

### Step-by-Step Installation

1. **Clone the Repository**
   ```bash
   git clone https://github.com/your-username/steg-ip-tool.git
   cd steg-ip-tool
   ```

2. **Set Up Virtual Environment**
   ```bash
   python -m venv venv
   
   # Activate on Linux/macOS
   source venv/bin/activate
   
   # Activate on Windows
   venv\Scripts\activate
   ```

3. **Install Dependencies**
   ```bash
   pip install -r requirements.txt
   ```

4. **Configure Environment**
   ```bash
   cp .env.example .env
   # Edit .env with your settings
   ```

5. **Initialize Database** (if using database features)
   ```bash
   alembic upgrade head
   ```

### Docker Installation (Recommended for Production)

```bash
# Build and run with Docker Compose
docker-compose up -d

# Check service status
docker-compose ps
```

---

## Usage

### Basic Steganography Examples

**Encode a message into an image:**

```python
from steganography_engine import PNGEncoder

# Load your image
encoder = PNGEncoder()
result = encoder.encode("path/to/image.png", "Your secret message")
result.save("output/encoded_image.png")
```

**Decode a message from an image:**

```python
from steganography_engine import PNGDecoder

decoder = PNGDecoder()
hidden_message = decoder.decode("path/to/encoded_image.png")
print(f"Hidden message: {hidden_message}")
```

### Using the API

**Encode via REST API:**

```bash
curl -X POST "http://localhost:8000/api/v1/steg/encode" \
  -H "Authorization: Bearer your-token" \
  -F "image=@your_image.png" \
  -F "message=Your secret message"
```

**Capture IP via REST API:**

```bash
curl -X POST "http://localhost:8000/api/v1/ip/capture" \
  -H "Authorization: Bearer your-token"
```

### Command Line Interface (CLI)

```bash
# Encode message
python -m cli encode --image input.png --message "Secret" --output output.png

# Decode message
python -m cli decode --image encoded.png

# Capture current IP
python -m cli capture-ip --format json
```

---

## Project Structure

```
steg-ip-tool/
├── docs/                          # Documentation
│   ├── TECHNICAL_SPECIFICATION.md # Technical details
│   ├── LOGGING_STRATEGY.md        # Logging documentation
│   ├── CODE_EXECUTION_PLAN.md     # Development roadmap
│   └── README_TEMPLATE.md         # This file
│
├── services/                      # Microservices
│   ├── steganography_engine/      # Image encoding/decoding
│   │   ├── encoders/              # Format-specific encoders
│   │   ├── decoders/              # Format-specific decoders
│   │   └── validators/            # Image validation
│   │
│   ├── ip_capture_service/        # IP logging service
│   │   ├── capture/               # IP extraction
│   │   ├── analytics/             # Reporting
│   │   └── geo/                   # Geo-location
│   │
│   └── orchestration_service/     # Workflow management
│       ├── workflows/             # Workflow definitions
│       ├── clients/               # Service clients
│       └── scheduler/             # Job scheduling
│
├── tests/                         # Test suites
│   ├── unit/                      # Unit tests
│   ├── integration/               # Integration tests
│   └── e2e/                       # End-to-end tests
│
├── scripts/                       # Utility scripts
├── docker/                        # Docker configurations
├── .github/                       # GitHub workflows
│
├── requirements.txt               # Python dependencies
├── docker-compose.yml             # Docker Compose config
├── .env.example                   # Environment template
└── README.md                      # Project README
```

---

## Configuration

### Environment Variables

Create a `.env` file with the following variables:

```env
# Application
APP_ENV=development
DEBUG=true
SECRET_KEY=your-secret-key-here

# Database
DATABASE_URL=postgresql://user:pass@localhost:5432/steg_tool

# Redis
REDIS_URL=redis://localhost:6379/0

# Services
STEGANOGRAPHY_SERVICE_URL=http://localhost:8001
IP_CAPTURE_SERVICE_URL=http://localhost:8002

# Logging
LOG_LEVEL=INFO
LOG_FORMAT=json

# IP Capture
IP_ANONYMIZE=true
IP_RETENTION_DAYS=30

# Optional: Geo-location
GEOIP_DATABASE_PATH=/path/to/GeoLite2-City.mmdb
```

### Configuration Files

| File | Purpose |
|------|---------|
| `.env` | Environment-specific settings |
| `config/default.yaml` | Default configuration |
| `config/production.yaml` | Production overrides |
| `config/development.yaml` | Development overrides |

---

## API Reference

### Steganography Engine

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/api/v1/steg/encode` | POST | Encode message into image |
| `/api/v1/steg/decode` | POST | Decode message from image |
| `/api/v1/steg/validate` | POST | Validate image for steganography |
| `/api/v1/steg/formats` | GET | List supported formats |
| `/api/v1/steg/health` | GET | Service health check |

### IP Capture Service

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/api/v1/ip/capture` | POST | Capture and log IP |
| `/api/v1/ip/lookup/{ip}` | GET | Lookup IP details |
| `/api/v1/ip/analytics` | GET | Get analytics data |
| `/api/v1/ip/export` | GET | Export logs |
| `/api/v1/ip/health` | GET | Service health check |

### Orchestration Service

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/api/v1/workflow/create` | POST | Create new workflow |
| `/api/v1/workflow/{id}` | GET | Get workflow status |
| `/api/v1/workflow/{id}/run` | POST | Execute workflow |
| `/api/v1/services/health` | GET | Check all services health |
| `/api/v1/jobs` | GET | List background jobs |

For detailed API documentation, visit `/docs` when running the server.

---

## Development

### Setting Up Development Environment

```bash
# Install development dependencies
pip install -r requirements-dev.txt

# Install pre-commit hooks
pre-commit install

# Run linting
flake8 .
black --check .
mypy .

# Run formatting
black .
isort .
```

### Code Style

We follow these conventions:
- **PEP 8** for Python code style
- **Black** for code formatting
- **Type hints** for all public functions
- **Docstrings** for all modules, classes, and functions

### Making Changes

1. Create a feature branch
2. Make your changes
3. Write/update tests
4. Run linting and tests
5. Submit a pull request

---

## Testing

### Running Tests

```bash
# Run all tests
pytest

# Run with coverage
pytest --cov=. --cov-report=html

# Run specific test file
pytest tests/unit/test_encoders.py

# Run with verbose output
pytest -v

# Run only fast tests
pytest -m "not slow"
```

### Test Structure

```
tests/
├── unit/                 # Fast, isolated tests
├── integration/          # Service interaction tests
├── e2e/                  # Full workflow tests
└── fixtures/             # Test data and helpers
```

---

## Deployment

### Using Docker Compose (Recommended)

```bash
# Production deployment
docker-compose -f docker-compose.prod.yml up -d

# View logs
docker-compose logs -f

# Scale services
docker-compose up -d --scale steganography=3
```

### Manual Deployment

```bash
# Install production dependencies
pip install -r requirements.txt

# Set environment variables
export APP_ENV=production
export DEBUG=false

# Run with Gunicorn
gunicorn orchestration_service.app:app -w 4 -k uvicorn.workers.UvicornWorker
```

### Health Checks

All services expose health endpoints:
- Steganography: `http://localhost:8001/health`
- IP Capture: `http://localhost:8002/health`
- Orchestration: `http://localhost:8000/health`

---

## Contributing

We welcome contributions! Here's how to get involved:

### Ways to Contribute

- 🐛 Report bugs
- 💡 Suggest features
- 📝 Improve documentation
- 🔧 Submit pull requests

### Contribution Process

1. **Fork** the repository
2. **Clone** your fork locally
3. **Create** a feature branch (`git checkout -b feature/amazing-feature`)
4. **Make** your changes
5. **Test** your changes thoroughly
6. **Commit** with clear messages (`git commit -m 'Add amazing feature'`)
7. **Push** to your fork (`git push origin feature/amazing-feature`)
8. **Open** a Pull Request

### Code of Conduct

Please read our [Code of Conduct](CODE_OF_CONDUCT.md) before contributing.

---

## FAQ

### General Questions

**Q: What image formats are supported?**
A: Currently, we support PNG, JPEG, and SVG. More formats are planned for future releases.

**Q: How much data can I hide in an image?**
A: It depends on the image size. Generally, you can hide about 1/8 of the total pixel data without noticeable quality loss.

**Q: Is this tool secure?**
A: The tool provides steganography (hiding data), not encryption. For sensitive data, we recommend encrypting your message before encoding.

### Technical Questions

**Q: Why is decoding failing?**
A: Ensure the image hasn't been modified, compressed, or converted after encoding. Lossy formats like JPEG may lose hidden data.

**Q: How do I scale for high traffic?**
A: Use the Docker Compose configuration with multiple service replicas behind a load balancer.

**Q: Can I use this with existing systems?**
A: Yes! Our REST API integrates with any system that can make HTTP requests.

---

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## Acknowledgments

- Thanks to all contributors
- Built with [FastAPI](https://fastapi.tiangolo.com/), [Pillow](https://pillow.readthedocs.io/), and [Stegano](https://pypi.org/project/stegano/)
- Inspired by the need for privacy-preserving tools

---

## Contact

- **Project Maintainer**: [Your Name](mailto:your.email@example.com)
- **Project Link**: [https://github.com/your-username/steg-ip-tool](https://github.com/your-username/steg-ip-tool)
- **Issue Tracker**: [GitHub Issues](https://github.com/your-username/steg-ip-tool/issues)

---

*Made with ❤️ for the open-source community*
