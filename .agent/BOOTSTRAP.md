---
description: Scripter Project Bootstrap Guide
---

# Scripter Project Bootstrap Guide

This document describes standard project initialization procedures that Scripter agent follows when starting a new project.

> **Note**: For operation guidelines, refer to [`AGENTS.md`](AGENTS.md).

---

##1. Project Overview

When starting a new script project, collect and define the following information:

- **Project Name**: Project identifier (lowercase English, hyphen recommended)
- **Project Purpose**: Core function that script will perform
- **Key Features**: List of features to implement
- **Execution Cycle**: Manual execution or automation (systemd timer, cron, etc.)
- **External Dependencies**: Required Python packages and system requirements

---

##2. Standard Project Structure

Scripter agent initializes projects using the following structure:

```
project-name/
├── src/                          # Python source code
│   ├── main.py                   # Main script (entry point)
│   ├── module1.py                # Feature module 1
│   └── module2.py                # Feature module 2
├── tests/                        # Test code
│   ├── __init__.py
│   ├── test_main.py              # Main script test
│   ├── test_module1.py           # Module 1 test
│   └── README.md                 # Test execution guide
├── scripts/                      # Execution and deployment scripts
│   ├── setup-env.sh              # Environment setup script
│   ├── install-systemd.sh        # Systemd installation script
│   ├── run.sh                    # Main execution script
│   └── systemd/                  # Systemd configuration files
│       ├── project-name.service  # Service definition
│       └── project-name.timer    # Timer definition
├── .venv/                        # Python virtual environment (created by setup-env.sh)
├── .env                          # Environment variables (private, created from .env.example)
├── .env.example                  # Environment variable template
├── requirements.txt              # Python dependency list
├── README.md                     # Project documentation
├── .gitignore                    # Git ignore file
└── CURRENT_STATE.md              # Session state snapshot (created when needed)
```

### 2.1. Required File Descriptions

| File | Description |
|-------|-------------|
| `requirements.txt` | Python package dependency list |
| `.env.example` | Environment variable template (excluding actual values) |
| `setup-env.sh` | venv creation, dependency installation, .env creation |
| `install-systemd.sh` | Systemd service/timer installation |
| `tests/` | unittest-based test code |
| `README.md` | Installation, usage, structure description |

---

##3. Project Initialization Steps

### 3.1. Directory Structure Creation

```bash
# Create project root directory
mkdir -p project-name/src
mkdir -p project-name/tests
mkdir -p project-name/scripts/systemd
```

### 3.2. Required File Creation

#### 3.2.1. `requirements.txt`
List required Python packages for the project:

```
# Project dependencies
python-dotenv >= 1.0.0
requests >= 2.31.0
```

#### 3.2.2. `.env.example`
Create an environment variable template. Do not include actual values:

```bash
# Environment variable example
# Copy to .env file and enter values when actually using

# API key or credentials
API_KEY=your_api_key_here
SECRET_KEY=your_secret_key_here

# Configuration values
CONFIG_OPTION=default_value
DEBUG=false
```

#### 3.2.3. `.gitignore`
Specify files not to track in Git:

```
# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python

# Virtual Environment
.venv/
venv/
ENV/

# Environment Variables
.env

# IDE
.vscode/
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# Project Specific
data/
logs/
*.log
```

### 3.3. Script File Creation

#### 3.3.1. `scripts/setup-env.sh`
Python virtual environment creation and dependency installation script:

```bash
#!/bin/bash
set -e

SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
PROJECT_DIR="$(cd "$SCRIPT_DIR/.." && pwd)"
VENV_DIR="$PROJECT_DIR/.venv"

echo "Project Name - Environment Setup"
echo "========================================"
echo "Project directory: $PROJECT_DIR"
echo ""

# Check Python
echo "Checking Python installation..."
if ! command -v python3 &> /dev/null; then
    echo "Error: Python 3 is not installed"
    exit 1
fi
echo "Found: $(python3 --version)"
echo ""

# Create venv
if [ ! -d "$VENV_DIR" ]; then
    echo "Creating virtual environment..."
    python3 -m venv "$VENV_DIR"
    echo "Virtual environment created"
else
    echo "Virtual environment already exists"
fi
echo ""

# Upgrade pip
echo "Upgrading pip..."
"$VENV_DIR/bin/pip" install --upgrade pip --quiet
echo "pip upgraded"
echo ""

# Install dependencies
echo "Installing Python dependencies..."
"$VENV_DIR/bin/pip" install -r "$PROJECT_DIR/requirements.txt"
echo "Dependencies installed"
echo ""

# Check .env file
if [ ! -f "$PROJECT_DIR/.env" ]; then
    echo "Creating .env from .env.example..."
    cp "$PROJECT_DIR/.env.example" "$PROJECT_DIR/.env"
    echo ".env file created"
    echo "Please edit .env and configure your settings"
else
    echo ".env file exists"
fi
echo ""

echo "Environment setup completed!"
```

#### 3.3.2. `scripts/install-systemd.sh`
Systemd service and timer installation script:

```bash
#!/bin/bash
set -e

SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
PROJECT_DIR="$(cd "$SCRIPT_DIR/.." && pwd)"
SYSTEMD_USER_DIR="$HOME/.config/systemd/user"
SERVICE_NAME="project-name"

echo "Project Name - Systemd Timer Installation"
echo "=============================================="
echo "Project directory: $PROJECT_DIR"
echo ""

# Load environment variables from .env
if [ -f "$PROJECT_DIR/.env" ]; then
    export $(cat "$PROJECT_DIR/.env" | grep -v '^#' | xargs)
    echo "Environment variables loaded from .env"
else
    echo "Warning: .env file not found"
fi

# Create systemd directory
echo "Setting up systemd timer..."
mkdir -p "$SYSTEMD_USER_DIR"

# Create service file with environment variables
echo "Creating $SERVICE_NAME.service..."
cat > "$SYSTEMD_USER_DIR/$SERVICE_NAME.service" << EOF
[Unit]
Description=Project Name Service
After=network-online.target
Wants=network-online.target

[Service]
Type=oneshot
WorkingDirectory=$PROJECT_DIR
Environment="PATH=$PROJECT_DIR/.venv/bin:/usr/local/bin:/usr/bin:/bin"
EnvironmentFile=$PROJECT_DIR/.env

ExecStart=$PROJECT_DIR/.venv/bin/python $PROJECT_DIR/src/main.py

StandardOutput=journal
StandardError=journal
SyslogIdentifier=project-name

[Install]
WantedBy=default.target
EOF

# Copy timer file
echo "Copying $SERVICE_NAME.timer..."
cp "$SCRIPT_DIR/systemd/$SERVICE_NAME.timer" "$SYSTEMD_USER_DIR/"

echo "Systemd files installed"
echo ""

# Reload systemd daemon
echo "Reloading systemd daemon..."
systemctl --user daemon-reload

# Enable and start timer
echo "Enabling and starting timer..."
systemctl --user enable "$SERVICE_NAME.timer"
systemctl --user start "$SERVICE_NAME.timer"

echo ""
echo "Timer installation completed!"
echo ""
echo "Useful commands:"
echo "  • Check timer status:   systemctl --user status $SERVICE_NAME.timer"
echo "  • Check service logs:   journalctl --user -u $SERVICE_NAME.service"
echo "  • Follow logs:          journalctl --user -u $SERVICE_NAME.service -f"
```

#### 3.3.3. `scripts/run.sh`
Main execution script:

```bash
#!/bin/bash
set -e

SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
PROJECT_DIR="$(cd "$SCRIPT_DIR/.." && pwd)"

# Load environment variables
if [ -f "$PROJECT_DIR/.env" ]; then
    export $(cat "$PROJECT_DIR/.env" | grep -v '^#' | xargs)
fi

# Activate virtual environment
source "$PROJECT_DIR/.venv/bin/activate"

# Run main script
python "$PROJECT_DIR/src/main.py" "$@"
```

### 3.4. Systemd Configuration Files

#### 3.4.1. `scripts/systemd/project-name.service`

```ini
[Unit]
Description=Project Name Service
After=network-online.target
Wants=network-online.target

[Service]
Type=oneshot
WorkingDirectory={{PROJECT_ROOT}}
Environment="PATH={{PROJECT_ROOT}}/.venv/bin:/usr/local/bin:/usr/bin:/bin"
EnvironmentFile={{PROJECT_ROOT}}/.env

ExecStart={{PROJECT_ROOT}}/.venv/bin/python {{PROJECT_ROOT}}/src/main.py

StandardOutput=journal
StandardError=journal
SyslogIdentifier=project-name

[Install]
WantedBy=default.target
```

#### 3.4.2. `scripts/systemd/project-name.timer`

```ini
[Unit]
Description=Project Name Timer
Requires=project-name.service

[Timer]
# Set execution cycle (e.g., daily at midnight)
OnCalendar=*-*-* 00:00:00
Persistent=true

[Install]
WantedBy=timers.target
```

**Timer Schedule Examples**:

| Pattern | Description |
|----------|-------------|
| `*-*-* 00:00:00` | Daily at midnight |
| `*-*-* 09:00:00` | Daily at 9 AM |
| `Mon *-*-* 09:00:00` | Every Monday at 9 AM |
| `Mon,Fri *-*-* 09:00:00` | Every Monday and Friday at 9 AM |
| `*-*-01 00:00:00` | Monthly on 1st at midnight |
| `Mon *-*-1,7,14,21 09:00:00` | Every 1st, 3rd, 4th, 5th Monday at 9 AM |

### 3.5. Test Files

#### 3.5.1. `tests/__init__.py`

```python
# Test package initialization
```

#### 3.5.2. `tests/test_main.py`

```python
import unittest
import sys
import os

# Add src to path
sys.path.insert(0, os.path.join(os.path.dirname(__file__), '..', 'src'))

class TestMain(unittest.TestCase):
    """Test cases for main.py"""

    def setUp(self):
        """Set up test fixtures"""
        pass

    def tearDown(self):
        """Clean up after tests"""
        pass

    def test_example(self):
        """Example test case"""
        self.assertTrue(True)

if __name__ == '__main__':
    unittest.main()
```

#### 3.5.3. `tests/README.md`

```markdown
# Project Name - Test Suite

## Running Tests

### Run All Tests
```bash
python -m unittest discover tests
```

### Run Specific Test File
```bash
python -m unittest tests.test_main
```

### Verbose Output
```bash
python -m unittest discover tests -v
```

## Test Structure

```
tests/
├── __init__.py
├── test_main.py
└── test_module1.py
```
```

### 3.6. Source Code Files

#### 3.6.1. `src/main.py`

```python
#!/usr/bin/env python3
"""
Project Name - Main Script

Description of what this script does.
"""

import os
from dotenv import load_dotenv

# Load environment variables
load_dotenv()

def main():
    """Main function"""
    print("Project Name - Running...")

    # Your code here

    print("Project Name - Completed!")

if __name__ == "__main__":
    main()
```

### 3.7. README.md

```markdown
# Project Name

Brief description of project.

## Features

- Feature 1
- Feature 2
- Feature 3

## Installation

### Prerequisites

- Python 3.9 or higher
- pip

### Setup

```bash
# Clone repository
git clone <repository-url>
cd project-name

# Run setup script
./scripts/setup-env.sh

# Configure environment variables
nano .env
```

## Usage

### Manual Execution

```bash
./scripts/run.sh
```

### Systemd Timer (Linux)

```bash
# Install systemd timer
./scripts/install-systemd.sh

# Check timer status
systemctl --user status project-name.timer

# View logs
journalctl --user -u project-name.service -f
```

## Project Structure

```
project-name/
├── src/              # Source code
├── tests/            # Test suite
├── scripts/          # Setup and deployment scripts
└── ...
```

## License

MIT License
```

---

##4. Initialization Checklist (Initialization Checklist)

Check the following items when initializing a project:

- [ ] Create project root directory
- [ ] Create `src/` directory
- [ ] Create `tests/` directory
- [ ] Create `scripts/` and `scripts/systemd/` directories
- [ ] Create `requirements.txt` and write dependencies
- [ ] Create `.env.example` and write environment variable template
- [ ] Create `.gitignore`
- [ ] Create `scripts/setup-env.sh` and grant execution permission (`chmod +x`)
- [ ] Create `scripts/install-systemd.sh` and grant execution permission (`chmod +x`)
- [ ] Create `scripts/run.sh` and grant execution permission (`chmod +x`)
- [ ] Create `scripts/systemd/project-name.service`
- [ ] Create `scripts/systemd/project-name.timer`
- [ ] Create `tests/__init__.py`
- [ ] Create `tests/test_main.py`
- [ ] Create `tests/README.md`
- [ ] Create `src/main.py`
- [ ] Create `README.md`
- [ ] Test `setup-env.sh` execution
- [ ] Test `run.sh` execution
- [ ] Test test execution (`python -m unittest discover tests`)

---

##5. Common Work Patterns (Common Patterns)

### 5.1. Adding New Module

```bash
# 1. Create source file
touch src/module_name.py

# 2. Create test file
touch tests/test_module_name.py

# 3. Write tests
# Write test cases in tests/test_module_name.py

# 4. Implement module
# Implement features in src/module_name.py

# 5. Run tests
python -m unittest tests.test_module_name
```

### 5.2. Adding New Dependency

```bash
# 1. Add package to requirements.txt
echo "package-name >= 1.0.0" >> requirements.txt

# 2. Install in virtual environment
.venv/bin/pip install -r requirements.txt
```

### 5.3. Changing Timer Schedule

```bash
# 1. Edit scripts/systemd/project-name.timer
nano scripts/systemd/project-name.timer

# 2. Modify OnCalendar value

# 3. Reinstall timer
./scripts/install-systemd.sh
```

---

##6. Additional Resources

- [`AGENTS.md`](AGENTS.md): Scripter agent operation guidelines
- [`TOOLS.md`](TOOLS.md): External tool usage guide
- [Python unittest documentation](https://docs.python.org/3/library/unittest.html)
- [systemd.timer documentation](https://man7.org/linux/man-pages/man5/systemd.timer.5.html)
