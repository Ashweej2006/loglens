# LogLens 🔍⚡

> **In-terminal triage pipeline for runtime error traces with boundary secret redaction, framework noise filtering, and deterministic root-cause remediation.**

Developers waste countless hours parsing verbose 150+ line backend stack traces in the terminal. Context-switching to web chats or search engines risks leaking sensitive production credentials (database connection strings, Bearer tokens, private API keys, and local home paths).

**LogLens** intercepts execution streams at the Unix pipe boundary, scrubs secrets, strips framework internals (e.g. Django, Flask, FastAPI, Starlette, SQLAlchemy), isolates the exact offending line in user repository code, and renders an actionable, color-coded diagnostic triage box directly in your terminal using `rich`.

---

## 🚀 Key Features

1. **The Standard Input Non-Blocking Boundary**:
   - Accidental direct execution (`loglens`) detects interactive TTY via `sys.stdin.isatty()` and instantly displays usage help without hanging or blocking.
2. **Deterministic Secret Redaction**:
   - Targeted regex masks database URIs (`postgres://user:pass@host`), Bearer/JWT tokens, API keys (`sk-...`, `AKIA...`, `ghp_...`), and user home paths (`/home/username` or `C:\Users\...`) while preventing false positives on common variables like `user_id` or `password_hash`.
3. **Framework Noise Filter**:
   - Automatically filters out internal `site-packages/`, `dist-packages/`, `<built-in>`, and stdlib frames, highlighting only the user's local code frame.
4. **Cross-Platform Source Resolution**:
   - Uses `pathlib.Path` normalization to resolve source context across Windows, Linux, macOS, and containerized paths.
5. **Deterministic Offline Diagnostics**:
   - Maps Python and backend framework exceptions (`KeyError`, `AttributeError`, `ModuleNotFoundError`, `OperationalError`, `IntegrityError`, `ValidationError`, etc.) to clear root causes and recommended code fixes.

---

## 📦 Installation

```bash
# Clone and install locally
git clone https://github.com/your-org/loglens.git
cd loglens
pip install -e .
```

---

## 🛠️ Usage

### 1. Pipe Standard Error / Streams
```bash
# FastAPI / Uvicorn
uvicorn app.main:app --reload 2>&1 | loglens

# Django Development Server
python manage.py runserver 2>&1 | loglens

# Python Scripts
python app.py 2>&1 | loglens

# Pytest Test Runs
pytest 2>&1 | loglens
```

### 2. Inspect Existing Log Files
```bash
loglens crash.log
# or
loglens -f /var/log/server_err.log
```

### 3. Run the Built-in Simulation Demo
```bash
loglens --demo
```

### 4. Raw Output Mode (for piping to clean log files)
```bash
python app.py 2>&1 | loglens --raw > sanitized.log
```

---

## 🏗️ Architecture & Module Map

| Module | Purpose |
|---|---|
| [`loglens/schema.py`](file:///C:/Users/Ashweej%20Pajithaya/.gemini/antigravity-ide/scratch/loglens/loglens/schema.py) | Strict dataclass contract (`ParsedError`, `StackFrame`, `CodeContext`, `DiagnosticFix`, `RedactionSummary`) |
| [`loglens/sanitizer.py`](file:///C:/Users/Ashweej%20Pajithaya/.gemini/antigravity-ide/scratch/loglens/loglens/sanitizer.py) | Targeted regex redaction engine with match metric counters |
| [`loglens/parser.py`](file:///C:/Users/Ashweej%20Pajithaya/.gemini/antigravity-ide/scratch/loglens/loglens/parser.py) | Traceback parser, framework noise stripper, and source code context extractor |
| [`loglens/diagnostics.py`](file:///C:/Users/Ashweej%20Pajithaya/.gemini/antigravity-ide/scratch/loglens/loglens/diagnostics.py) | Offline deterministic root cause engine and remediation mapper |
| [`loglens/ui.py`](file:///C:/Users/Ashweej%20Pajithaya/.gemini/antigravity-ide/scratch/loglens/loglens/ui.py) | Terminal rendering with syntax highlighting, badges, and colored panels |
| [`loglens/cli.py`](file:///C:/Users/Ashweej%20Pajithaya/.gemini/antigravity-ide/scratch/loglens/loglens/cli.py) | CLI argument parser, non-blocking TTY verification, and pipeline orchestration |
| [`tests/test_pipeline.py`](file:///C:/Users/Ashweej%20Pajithaya/.gemini/antigravity-ide/scratch/loglens/tests/test_pipeline.py) | Full test suite validating redaction, parsing, normalization, and diagnostics |

---

## 🧪 Running Tests

```bash
pytest tests/ -v
```
