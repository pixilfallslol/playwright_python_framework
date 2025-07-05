# Playwright Python Testing Framework

A modular, pytest-based Playwright framework for testing React applications (or any web apps) in Python. Built-in support for:

* **Page Object Model** (POM) for clean, maintainable test code
* **Data-driven testing** via JSON fixtures
* **Visual regression** with baseline snapshots and automatic diffs
* **Screenshots & videos** on failures for easier debugging
* **Environment configuration** using `.env` files
* **Authenticated session reuse** using Playwright's `storageState`
* **HTML & JUnit reporting** via `pytest-html` and JUnit XML
* **Optional Allure reporting** for advanced dashboards
* **CLI & CI integration** with GitHub Actions and GitHub Pages

---

## 🧩 Git Hooks with Husky (Recommended)

Integrating [Husky](https://github.com/typicode/husky) ensures code quality before changes are committed. Although Husky is a Node.js tool, it works great for Python projects too.

### 🚀 Setup

1. Initialize Husky in your repo (requires Node.js):

   ```bash
   npm init -y
   npm install husky --save-dev
   npx husky install
   ```

2. Add the install command to your `package.json`:

   ```json
   "scripts": {
     "prepare": "husky install"
   }
   ```

3. Create a pre-commit hook to run your tests and linters:

   ```bash
   npx husky add .husky/pre-commit "pytest tests/ && black --check . && flake8"
   git add .husky/pre-commit
   ```

---

## 🧰 Getting Started (For Beginners)

If you're new to QA or test automation, follow these steps to get this framework running on your machine.

### ✅ Prerequisites

- Python 3.8 or later installed
- Git installed
- Node.js installed (for optional Husky Git hooks)
- Google Chrome (for web automation visibility)

---

### 🪜 Step-by-Step Setup

#### 1. **Clone the Repository**

```bash
git clone https://github.com/your-username/your-python-repo.git
cd your-python-repo
```

#### 2. **Create a Virtual Environment**

```bash
python -m venv .venv
source .venv/bin/activate  # For macOS/Linux
# .\.venv\Scripts\activate  # For Windows
```

#### 3. **Install Python Dependencies**

```bash
pip install -r requirements.txt
playwright install
```

#### 4. **Create Your `.env` File**

Copy the example file and edit it:

```bash
cp .env.example .env
```

Update `.env` with your app URL and login credentials:

```env
BASE_URL=https://your-app.com
LOGIN_EMAIL=your@email.com
LOGIN_PASSWORD=yourpassword
```

#### 5. **Generate Login Session (One Time)**

This will log in and save a session token to reuse in tests:

```bash
python utils/save_storage_state.py
```

#### 6. **Run the Tests**

```bash
pytest
```

#### 7. **View the Test Report**

Open the HTML report in your browser:

```bash
open reports/html/report.html  # Mac
start reports\html\report.html  # Windows
```

---

### 🛠 (Optional) Enable Git Hooks with Husky

Husky helps make sure your code is clean before committing.

```bash
npm install
npx husky install
npx husky add .husky/pre-commit "pytest && black --check . && flake8"
```

Now, every time you commit, it will check that your tests pass and code is formatted.

---

### 🚀 You’re Ready!

Now you can start:

- Writing tests inside the `tests/` folder
- Creating reusable components in `pages/` using Page Object Model
- Using data files in `fixtures/data/`
- Viewing visual regression screenshots in `visual_regression/`

---

### 🤖 Common Commands

| Action                    | Command                          |
|---------------------------|----------------------------------|
| Run all tests             | `pytest`                         |
| Run one test file         | `pytest tests/test_signup.py`   |
| Regenerate login session  | `python utils/save_storage_state.py` |
| View HTML report          | Open `reports/html/report.html` |

---

> 🔰 This project is beginner-friendly. If you follow the steps above, you’ll be up and running with a professional-grade test framework in minutes — no deep experience needed.
