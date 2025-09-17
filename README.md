# Chat GPT from Zero



**Project Setup with `uv` + Jupyter (VS Code friendly)**

This repo uses **[uv](https://docs.astral.sh/uv/)** for Python environments, dependency management, and fast installs. You’ll get:

* a local virtual environment (`.venv`)
* reproducible installs via `pyproject.toml` + `uv.lock`
* Jupyter & VS Code support with a named kernel

## Prerequisites

* macOS with a recent Xcode CLT and Git
* **uv** installed (any one method):

  ```bash
  # Homebrew
  brew install uv

  # or official installer
  curl -LsSf https://astral.sh/uv/install.sh | sh
  ```

  Why: uv is a fast, all-in-one Python package & project manager.

---

## 1) Initialize the project

```bash
# from your project folder (or create one)
mkdir my-project && cd my-project

# bootstrap a uv project (creates pyproject.toml and .venv on demand)
uv init .
```

**Why:** `uv init` creates a standard Python project with `pyproject.toml` so dependencies are tracked and can be locked for reproducibility. 

---

## 2) (Optional) Pin your Python and create the venv now

```bash
# install and prefer a specific Python (e.g., 3.12)
uv python install 3.12
uv venv --python 3.12
```

**Why:** Ensures everyone uses the same Python version and creates a local `.venv`. uv can also auto-create the venv when needed.

---

## 3) Jupyter support (kernel + VS Code)

### 3a) Add Jupyter kernel support to the project

```bash
uv add --dev ipykernel
```

**Why:** VS Code and Jupyter need `ipykernel` inside your project environment to run notebooks and attach the kernel. Keeping it as a **dev** dependency keeps runtime deps clean.

### 3b) Create a named kernel for this project

```bash
# name it after your project (adjust "my-project" to your repo name)
uv run ipython kernel install --user --env VIRTUAL_ENV "$(pwd)/.venv" --name=chatgpt-from-zero
```

**Why:** This registers a kernel that points to your project’s `.venv`, so notebooks use the correct environment.

### 3c) Start Jupyter (two ways)

* **From terminal:**

  ```bash
  uv run --with jupyter jupyter lab
  ```

  **Why:** Runs Jupyter in an isolated tool env while your notebooks use the project kernel you created. Clean and reproducible.

* **From VS Code:**

  * Open the folder in VS Code → create a new Notebook.
  * When prompted for a kernel, choose **Python Environments** → select your `.venv` (or the `my-project` kernel you created).
    **Why:** VS Code discovers kernels from your project; `ipykernel` must be present. ([Astral Docs][2])

> Tip: If you ever see “Requires ipykernel”, just ensure step **3a** is done.

---

## 4) Add your project packages

Add runtime libraries (install + lock in one go):

```bash
# examples — add what you actually need
uv add numpy pandas matplotlib scikit-learn
```

Add dev tools (kept separate from runtime):

```bash
uv add --dev ruff black pytest
```

**Why:** `uv add` writes to `pyproject.toml` and updates `uv.lock`. Dev deps live in a separate group so they can be excluded in production if needed. ([Astral Docs][4])

---

## 5) Sync (install everything from lockfile)

On any machine (fresh clone or CI):

```bash
uv sync          # installs runtime + dev
# or
uv sync --no-dev # installs runtime only
```

**Why:** `uv sync` recreates the environment exactly from `uv.lock` for reproducible installs. ([Astral Docs][5])

---

## 6) Working in notebooks

* Install a new package **and track it** in the project (from a notebook cell):

  ```python
  !uv add pydantic
  ```
* Install **without** changing project files (temporary in the venv):

  ```python
  !uv pip install pydantic
  ```

**Why:** `!uv add` persists to `pyproject.toml`/`uv.lock`; `!uv pip install` is ad-hoc for quick experiments. ([Astral Docs][2])

---

## 7) Exporting requirements.txt (optional)

If some pipelines still need a `requirements.txt`:

```bash
uv pip compile pyproject.toml -o requirements.txt
```

**Why:** Generates a pinned `requirements.txt` from your project config; handy for legacy tooling. ([Astral Docs][6])

---

## 8) Quick start (copy/paste)

```bash
# 1) install uv (see top), then:
uv init .
uv python install 3.12
uv venv --python 3.12

# 2) jupyter kernel
uv add --dev ipykernel
uv run ipython kernel install --user --env VIRTUAL_ENV "$(pwd)/.venv" --name=my-project

# 3) add your libs
uv add numpy pandas matplotlib scikit-learn

# 4) start notebooks
uv run --with jupyter jupyter lab
```

---

## Troubleshooting

* **VS Code says “ipykernel required”** → run `uv add --dev ipykernel` and reopen the notebook. ([Astral Docs][2])
* **New machine setup** → `uv sync` (optionally `--no-dev`), then pick the `my-project` kernel or `.venv` in VS Code. ([Astral Docs][5])

---

If you want, tell me your project name and the exact libraries you use and I’ll generate this README pre-filled, plus a starter `pyproject.toml` and `.vscode/settings.json` tuned for notebooks.

[1]: https://formulae.brew.sh/formula/uv?utm_source=chatgpt.com "uv — Homebrew Formulae"
[2]: https://docs.astral.sh/uv/guides/integration/jupyter/ "Using uv with Jupyter | uv"
[3]: https://docs.astral.sh/uv/guides/install-python/?utm_source=chatgpt.com "Installing and managing Python | uv - Astral Docs"
[4]: https://docs.astral.sh/uv/concepts/projects/dependencies/?utm_source=chatgpt.com "Managing dependencies | uv - Astral Docs"
[5]: https://docs.astral.sh/uv/concepts/projects/sync/?utm_source=chatgpt.com "Locking and syncing | uv - Astral Docs"
[6]: https://docs.astral.sh/uv/pip/compile/?utm_source=chatgpt.com "Locking environments | uv - Astral Docs"
