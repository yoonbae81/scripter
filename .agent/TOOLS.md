---
description: External Tool Usage Guide
---

# TOOLS: External Tool Usage Guide

This document defines usage guides for external tools used by Scripter agent.

---

##1. Development Tools

### 1.1. Python

**Version**: Python 3.x

**Purpose**: Script development and execution

**Key Commands**:
```bash
# Check version
python3 --version

# Run script
python3 script.py

# Create virtual environment
python3 -m venv .venv

# Install package
pip install package-name
```

**Documentation**: [Python Official Docs](https://docs.python.org/3/)

---

### 1.2. pip

**Purpose**: Python package management

**Key Commands**:
```bash
# Install package
pip install package-name

# Upgrade package
pip install --upgrade package-name

# Uninstall package
pip uninstall package-name

# Install from requirements.txt
pip install -r requirements.txt

# List installed packages
pip list
```

**Documentation**: [pip Official Docs](https://pip.pypa.io/)

---

### 1.3. venv

**Purpose**: Python virtual environment creation

**Key Commands**:
```bash
# Create virtual environment
python3 -m venv .venv

# Activate virtual environment (macOS/Linux)
source .venv/bin/activate

# Deactivate virtual environment
deactivate
```

**Documentation**: [venv Official Docs](https://docs.python.org/3/library/venv.html)

---

##2. Testing Tools

### 2.1. unittest

**Purpose**: Python testing framework

**Key Commands**:
```bash
# Run all tests
python -m unittest discover tests

# Run specific test file
python -m unittest tests.test_main

# Verbose output
python -m unittest discover tests -v

# Run specific test case
python -m unittest tests.test_main.TestMain.test_example
```

**Test File Structure**:
```python
import unittest

class TestMain(unittest.TestCase):
    def setUp(self):
        pass

    def tearDown(self):
        pass

    def test_example(self):
        self.assertTrue(True)

if __name__ == '__main__':
    unittest.main()
```

**Documentation**: [unittest Official Docs](https://docs.python.org/3/library/unittest.html)

---

##3. Automation Tools

### 3.1. systemd (user-level)

**Purpose**: Linux/macOS system service and timer management

**Key Commands**:
```bash
# Reload daemon
systemctl --user daemon-reload

# Enable timer
systemctl --user enable service-name.timer

# Start timer
systemctl --user start service-name.timer

# Check timer status
systemctl --user status service-name.timer

# Check service logs
journalctl --user -u service-name.service

# Follow logs in real-time
journalctl --user -u service-name.service -f
```

**Service File Location**: `~/.config/systemd/user/`

**Documentation**: [systemd.timer Official Docs](https://man7.org/linux/man-pages/man5/systemd.timer.5.html)

---

##4. Version Control

### 4.1. Git

**Purpose**: Version control

**Key Commands**:
```bash
# Initialize repository
git init

# Add file
git add filename

# Commit
git commit -m "commit message"

# Add remote repository
git remote add origin url

# Push
git push origin main

# Pull
git pull origin main

# Check status
git status

# Check logs
git log
```

**Documentation**: [Git Official Docs](https://git-scm.com/doc)

---

##5. Web Scraping Tools

### 5.1. Playwright

**Purpose**: Browser automation and scraping

**Key Usage**:
```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch()
    page = browser.new_page()
    page.goto('https://example.com')
    # Scraping logic
    browser.close()
```

**Documentation**: [Playwright Official Docs](https://playwright.dev/python/)

---

### 5.2. BeautifulSoup

**Purpose**: HTML parsing

**Key Usage**:
```python
from bs4 import BeautifulSoup

soup = BeautifulSoup(html_content, 'html.parser')
elements = soup.find_all('div', class_='example')
```

**Documentation**: [BeautifulSoup Official Docs](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)

---

### 5.3. requests

**Purpose**: HTTP requests

**Key Usage**:
```python
import requests

response = requests.get('https://example.com')
print(response.text)
```

**Documentation**: [requests Official Docs](https://requests.readthedocs.io/)

---

##6. Environment Variable Management

### 6.1. python-dotenv

**Purpose**: Load environment variables from .env file

**Key Usage**:
```python
from dotenv import load_dotenv
import os

load_dotenv()

api_key = os.getenv('API_KEY')
```

**Documentation**: [python-dotenv Official Docs](https://github.com/theskumar/python-dotenv)

---

##7. Other Tools

### 7.1. curl

**Purpose**: HTTP requests (CLI)

**Key Commands**:
```bash
# GET request
curl https://example.com

# POST request
curl -X POST -d "data=value" https://example.com

# Include headers
curl -H "Content-Type: application/json" https://example.com
```

**Documentation**: [curl Official Docs](https://curl.se/docs/)

---

### 7.2. jq

**Purpose**: JSON data processing (CLI)

**Key Commands**:
```bash
# Parse JSON
echo '{"key": "value"}' | jq '.key'

# Filter array
echo '[1, 2, 3]' | jq '.[] | select(. > 1)'
```

**Documentation**: [jq Official Docs](https://stedolan.github.io/jq/)

---

##8. Tool Selection Guide

| Task | Recommended Tool | Alternative |
|------|------------------|-------------|
| Web Scraping (static) | requests + BeautifulSoup | urllib3, lxml |
| Web Scraping (dynamic) | Playwright | Selenium, Puppeteer |
| HTTP Requests | requests | urllib3, httpx |
| HTML Parsing | BeautifulSoup | lxml, html.parser |
| JSON Processing | jq (CLI), json (Python) | yq |
| Testing | unittest | pytest, nose2 |
| Automation | systemd | cron, launchd |

---

##9. Additional Resources

- [Python Official Docs](https://docs.python.org/3/)
- [PyPI (Python Package Index)](https://pypi.org/)
- [Awesome Python](https://awesome-python.com/)
