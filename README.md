# Playwright Python Testing Framework

A modular, pytest-based Playwright framework for testing React applications (or any web apps) in Python. Built-in support for:

* **Page Object Model** (POM) for clean, maintainable test code
* **Data-driven testing** via JSON fixtures
* **Visual regression** with baseline snapshots and automatic diffs
* **Screenshots & videos** on failures for easier debugging
* **Environment configuration** using `.env` files
* **HTML & JUnit reporting** via `pytest-html` and JUnit XML
* **Optional Allure reporting** for advanced dashboards
* **CLI & CI integration** with GitHub Actions and GitHub Pages

---

## 📦 Installation & Setup

All dependencies are tracked in `requirements.txt`. After cloning:

```bash
cd your-python-repo
python -m venv .venv       # create virtual environment
source .venv/bin/activate  # (macOS/Linux)
# .\.venv\Scripts\activate  # (Windows)
pip install -r requirements.txt
playwright install         # download browser binaries
```

You can add a helper script in `setup.sh` or as a Makefile target:

```makefile
setup:
	pip install -r requirements.txt && playwright install
```

---

## 🚀 Quickstart

1. **Configure**

   * Copy `.env.example` to `.env`
   * Edit `BASE_URL` in `.env` (e.g. `BASE_URL=https://faruk-hasan.com/automation`)

2. **Run tests**

   ```bash
   pytest
   ```

3. **View reports**

   * HTML report: open `reports/html/report.html`
   * JUnit XML: check `reports/junit/results.xml`
   * (Optional) Allure: run `allure serve reports/allure-results`

---

## 📁 Project Structure

```text
my_playwright_py_framework/
├── fixtures/
│   ├── data/               # JSON for data-driven tests (login_data.json)
│   └── conftest.py         # pytest fixtures (env, base_url)
├── pages/
│   └── signup_page.py      # Page Object Model classes
├── tests/
│   └── test_signup.py      # E2E, data-driven & visual-spec tests
├── visual_regression/
│   ├── baseline/           # golden screenshots
│   └── diffs/              # diff images on mismatch
├── reports/
│   ├── html/               # pytest-html report
│   └── junit/              # JUnit XML results
├── .github/workflows/ci.yml# CI pipeline
├── pytest.ini              # pytest configuration
├── requirements.txt        # Python dependencies
├── .env.example            # Environment variable template
└── README.md               # (this file)
```

---

## ✨ Key Capabilities

### Pytest Fixtures

Leverage `pytest` fixtures in `fixtures/conftest.py` to share setup across tests:

By default, `pytest-playwright` provides `browser`, `context`, and `page` fixtures. However, you can customize your own setup, for example:

```python
# fixtures/conftest.py
import os
import pytest
from dotenv import load_dotenv
from playwright.sync_api import sync_playwright

# Auto-load .env variables
@pytest.fixture(scope="session", autouse=True)
def load_env():
    load_dotenv(dotenv_path=".env")

# Provide the base URL
@pytest.fixture(scope="session")
def base_url():
    return os.getenv("BASE_URL")

# Custom browser fixture (overrides pytest-playwright's default)
@pytest.fixture(scope="session")
def browser():
    with sync_playwright() as p:
        browser = p.chromium.launch(headless=False)
        yield browser
        browser.close()

# Custom page fixture using the above browser
@pytest.fixture(scope="function")
def page(browser):
    page = browser.new_page()
    yield page
    page.close()
```

* You can mix and match built-in (`page`, `browser`) and custom fixtures.
* Name collisions override built-ins, so ensure fixture names align with your intended behavior.

### Page Object Model

Keep selectors and actions in classes under `pages/`:

### Page Object Model

Keep selectors and actions in classes under `pages/`:

```python
# pages/signup_page.py
from playwright.sync_api import Page

class SignupPage:
    def __init__(self, page: Page, base_url: str):
        self.page = page
        self.base_url = base_url
        self.email = page.locator('#email')
        self.password = page.locator('#password')
        self.submit_btn = page.locator("button[type='submit']")

    def goto(self):
        self.page.goto(f"{self.base_url}/signup.html")

    def fill_form(self, email: str, password: str):
        self.email.fill(email)
        self.password.fill(password)

    def submit(self):
        self.submit_btn.click()
```

### Data-Driven Testing

Load JSON test data from `fixtures/data`:

```python
# fixtures/data_loader.py
import json
from pathlib import Path

def load_json(filename: str):
    path = Path(__file__).parent / 'data' / filename
    return json.loads(path.read_text())
```

Use in tests with `pytest.mark.parametrize`:

```python
# tests/test_signup.py
import pytest
from pages.signup_page import SignupPage
from fixtures.data_loader import load_json

cases = load_json('login_data.json')

@pytest.mark.parametrize('case', cases)
def test_signup(page, base_url, case):
    signup = SignupPage(page, base_url)
    signup.goto()
    signup.fill_form(case['email'], case['password'])
    signup.submit()
    # assertions...
```

### Visual Regression

Capture and compare screenshots:

```python
def test_visual(page, base_url):
    page.goto(f"{base_url}/signup.html")
    page.expect_page().to_have_screenshot(path='visual_regression/baseline/signup_form.png')
```

### Reporting

* **pytest-html**: configured in `pytest.ini`, generates `reports/html/report.html`.
* **JUnit XML**: `--junitxml=reports/junit/results.xml` for CI integrations.
* **Allure** (optional): emit with `--alluredir=reports/allure-results` and serve via `allure serve`.

---

## 🌐 GitHub Pages Deployment

Host your pytest HTML reports on GitHub Pages with these steps:

1. **Enable Pages**

   * In your GitHub repo, go to **Settings → Pages**.
   * Under **Source**, select the branch (`main` or `gh-pages`) and folder (`/root` or `/docs`).

2. **Publish Reports**

   * **Using `docs/` folder**: copy your HTML report into `docs/reports/html` so Pages serves it:

     ```yaml
     - name: Publish pytest-html report
       run: |
         mkdir -p docs/reports/html
         cp -R reports/html/* docs/reports/html/
     ```
   * **Direct deployment**: use `peaceiris/actions-gh-pages` to push from `reports/html`:

     ```yaml
     - name: Deploy Reports to GitHub Pages
       uses: peaceiris/actions-gh-pages@v3
       with:
         publish_dir: docs/reports/html
         publish_branch: gh-pages
     ```

3. **Access Reports**
   Open at `https://<username>.github.io/<repo>/reports/html/` or `.../reports/html/index.html`

---

## 🛠 Development Workflow

Follow these steps to contribute and push changes without committing your virtual environment:

1. **Activate your virtual environment**

   ```bash
   source .venv/bin/activate   # macOS/Linux
   # .\venv\Scripts\activate  # Windows
   ```

2. **Create a feature branch**

   ```bash
   git checkout -b feature/your-description
   ```

3. **Make code changes**

   * Add tests in `tests/`, POMs in `pages/`, or fixtures in `fixtures/`.

4. **Stage and commit**

   ```bash
   git add .                  # excludes files in .gitignore (e.g. .venv/)
   git commit -m \"feat: Your summary of changes\"
   ```

5. **Push your branch**

   ```bash
   git push -u origin feature/your-description
   ```

6. **Open a Pull Request**

   * On GitHub, navigate to your fork and click **Compare & pull request**.
   * Fill in the PR template and submit for review.

> **Tip:** Ensure `.venv/`, `.pytest_cache/`, and other local artifacts are listed in `.gitignore` so they aren’t committed.

## 🤝 Contributing

1. Fork this repo
2. Add or update fixtures in `fixtures/`
3. Extend POMs under `pages/`
4. Write tests in `tests/`
5. Ensure `pytest` passes and reports generate
6. Submit a pull request

---

> Happy testing with Python & Playwright! 🐍🧪
