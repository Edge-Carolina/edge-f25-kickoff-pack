# Tooling Setup & Verification

Pick **one** path (Apprentice *or* Core). Everyone also needs a GitHub account and Git.

---

## 0) Common Prerequisites

* **GitHub account:** [https://github.com](https://github.com) (log in and verify email)
* **Git (CLI):**

  * **macOS (Homebrew):**

    ```bash
    brew install git
    git --version
    ```
  * **Windows (Winget):**

    ```powershell
    winget install --id Git.Git -e
    git --version
    ```
  * **Linux (Debian/Ubuntu):**

    ```bash
    sudo apt update && sudo apt install -y git
    git --version
    ```

---

## Path A — Apprentice (Python 3.11 + VS Code)

### A1) Install

* **Python 3.11**

  * **macOS (Homebrew):**

    ```bash
    brew install python@3.11
    python3 --version
    ```

    If `python3` points to another version, use the full path from `brew info python@3.11`.
  * **Windows:** download and install **Python 3.11.x** (check “Add python.exe to PATH”):
    [https://www.python.org/downloads/release/python-3110/](https://www.python.org/downloads/release/python-3110/)

    ```powershell
    py -3.11 --version
    ```
  * **Linux (Debian/Ubuntu):**

    ```bash
    sudo apt update && sudo apt install -y python3.11 python3.11-venv python3-pip
    python3.11 --version
    ```

* **VS Code**

  * Download: [https://code.visualstudio.com](https://code.visualstudio.com)
  * Recommended extensions: **Python** (Microsoft), **Pylance**

### A2) Verify Environment

> Goal: prove Python 3.11 runs, a virtual environment works, and `numpy` installs.

* **macOS/Linux:**

  ```bash
  python3 --version
  pip3 --version

  # Create and activate a virtual environment
  python3 -m venv .venv
  source .venv/bin/activate

  # Upgrade pip and install numpy
  python -m pip install --upgrade pip
  pip install numpy

  # Quick check
  python -c "import sys, numpy as np; print(sys.version); print('numpy', np.__version__)"
  ```

* **Windows:**

  ```powershell
  py -3.11 --version
  py -3.11 -m venv .venv
  .\.venv\Scripts\activate
  python -m pip install --upgrade pip
  pip install numpy
  python -c "import sys, numpy as np; print(sys.version); print('numpy', np.__version__)"
  ```

**Expected:** You see a Python `3.11.x` line and a `numpy` version (e.g., `2.x.x`).

### A3) Common Pitfalls (Python)

* **Activation fails on Windows:** run PowerShell as Administrator once:

  ```powershell
  Set-ExecutionPolicy RemoteSigned
  ```

  Then re-activate: `.\.venv\Scripts\activate`
* **macOS using the wrong Python:** run `which -a python3` and prefer the `python@3.11` path from Homebrew.
* **gcc/headers errors on Linux:** `sudo apt install -y build-essential python3.11-dev`

---

## Path B — Core (Node 18+ + Cursor)

### B1) Install Node 18+

* **Recommended (cross-platform): `nvm`**

  * **macOS/Linux:**

    ```bash
    # Install nvm
    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
    # Restart terminal, then:
    nvm install 18
    nvm use 18
    node -v
    npm -v
    ```
  * **Windows:** use **nvm-windows**

    * Download: [https://github.com/coreybutler/nvm-windows/releases](https://github.com/coreybutler/nvm-windows/releases)
    * After install:

      ```powershell
      nvm install 18
      nvm use 18
      node -v
      npm -v
      ```

* **Alternative installers**

  * **macOS (Homebrew):**

    ```bash
    brew install node@18
    echo 'export PATH="/opt/homebrew/opt/node@18/bin:$PATH"' >> ~/.zshrc
    exec zsh
    node -v
    npm -v
    ```
  * **Windows/MSI:** [https://nodejs.org](https://nodejs.org) (choose LTS ≥ 18)

### B2) Install Cursor

* Download: [https://www.cursor.com/](https://www.cursor.com/)
* Sign in; allow it to read your local repo for AI features.

### B3) Verify / Starter Project

* **Check versions:**

  ```bash
  node -v
  npm -v
  ```

* **Starter (choose one):**

  **Vanilla JS (no bundler)**

  ```bash
  mkdir ship0 && cd ship0
  npm init -y

  # Create a minimal index.html that uses localStorage
  cat > index.html <<'HTML'
  <!doctype html>
  <html>
    <head>
      <meta charset="utf-8">
      <title>Edge Labs — Ship Log</title>
    </head>
    <body>
      <h1>Edge Labs — Ship Log</h1>
      <input id="txt" placeholder="What did you ship?">
      <button id="add">Add</button>
      <ul id="list"></ul>
      <script>
        const KEY = "edge_ship_log";
        const t = document.getElementById("txt");
        const l = document.getElementById("list");

        function render(d = JSON.parse(localStorage.getItem(KEY) || "[]")) {
          l.innerHTML = d.map(x => `<li>${new Date(x.t).toLocaleString()} — ${x.v}</li>`).join("");
        }

        document.getElementById("add").onclick = () => {
          const v = t.value.trim();
          if (!v) return;
          const d = JSON.parse(localStorage.getItem(KEY) || "[]");
          d.unshift({ t: Date.now(), v });
          localStorage.setItem(KEY, JSON.stringify(d));
          t.value = "";
          render(d);
        };

        render();
      </script>
    </body>
  </html>
  HTML

  # Open it
  open index.html 2>/dev/null || xdg-open index.html || start index.html
  ```

  **Vite + React**

  ```bash
  npm create vite@latest ship0 -- --template react
  cd ship0
  npm install
  npm run dev
  # Open the localhost URL shown in the terminal
  ```

### B4) Common Pitfalls (Node)

* **Port already in use:** kill dev server or choose a new port (`npm run dev -- --port 5174`).
* **Weird dependency errors:** delete `node_modules` and `package-lock.json`, then `npm install`.
* **Global npm permissions (Linux):** use `nvm` to avoid `sudo npm install -g` issues.

---

## 1) Quick Git Check (both paths)

```bash
git config --global user.name "Your Name"
git config --global user.email "you@unc.edu"
git --version
```

If you’ll use SSH with GitHub:

```bash
# Generate a key (press Enter for defaults)
ssh-keygen -t ed25519 -C "you@unc.edu"

# Print the public key and add it to GitHub → Settings → SSH and GPG keys → New SSH key
cat ~/.ssh/id_ed25519.pub

# Test
ssh -T git@github.com
```

---

## 2) Troubleshooting Index

* **Python venv on Windows:** `py -3.11 -m venv .venv` then `.\.venv\Scripts\activate`
* **Numpy wheel fails on Linux:** `pip install --upgrade pip setuptools wheel`
* **Node version mismatch:** `nvm use 18` (or reinstall with `nvm install 18`)
* **Cursor won’t detect project:** open the folder root (where `package.json` or your code lives), not a parent directory.

---

## 3) What to Submit for Ship 0

* **Apprentice:** `env_check.py` output + screenshot; see `04_Ship_Check_0.md`
* **Core:** LocalStorage “Ship Log” app + screenshot; see `04_Ship_Check_0.md`

Complete the **Ship Check 0 Submission Form** by the deadline listed in `README.md`.
