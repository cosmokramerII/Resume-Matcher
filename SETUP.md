# Local Setup Guide for Resume-Matcher

![installing_resume_matcher](assets/how_to_install_resumematcher.png)

This document provides cross-platform instructions to get the project up and running locally.

---

## 🚀 Quickstart

### For Windows (PowerShell)

```powershell
# 1. Run the PowerShell setup script
.\setup.ps1

# 2. (Optional) Start the development server
.\setup.ps1 -StartDev
```

### For Linux/macOS (Bash)

```bash
# 1. Make the scripts executable
chmod +x setup.sh

# 2. Configure your environment and install dependencies
./setup.sh

# 3. (Optional) Start the development server
./setup.sh --start-dev
# or via Makefile
make setup
make run-dev
```

---

## 🛠️ Prerequisites

### Windows
- **PowerShell** 5.1 or later
- **Node.js** ≥ v18 (includes `npm`)
- **Python** ≥ 3.8 (`python3`, `pip3`)
- **LM Studio** (download from [https://lmstudio.ai/](https://lmstudio.ai/))
- **uv** (will be auto-installed by setup.ps1 if missing)

### Linux/macOS
- **Bash** 4.4 or higher
- **Node.js** ≥ v18 (includes `npm`)
- **Python** ≥ 3.8 (`python3`, `pip3`)
- **LM Studio** (download from [https://lmstudio.ai/](https://lmstudio.ai/))
- **curl** (for installing uv)
- **make** (for Makefile integration)

### Installing Prerequisites

**On Windows:**
You can install missing tools via Windows Package Manager (winget) or manual downloads:

```powershell
# Install Node.js via winget
winget install OpenJS.NodeJS

# Install Python via winget
winget install Python.Python.3.12
```

**Or download manually from official sites:**
- **Node.js**: Download from [https://nodejs.org/](https://nodejs.org/) (LTS version recommended)
- **Python**: Download from [https://www.python.org/downloads/](https://www.python.org/downloads/) (v3.8+ required)
- **LM Studio**: Download from [https://lmstudio.ai/](https://lmstudio.ai/) (available for Windows, macOS, and Linux)

**On macOS**, you can install missing tools via Homebrew:

```bash
brew update
brew install node python3 curl make
```

**On Linux** (Debian/Ubuntu):

```bash
sudo apt update && sudo apt install -y bash nodejs npm python3 python3-pip curl make
```

---

## 🔧 Environment Configuration

The project uses `.env` files at two levels:

1. **Root `.env`** — copied from `./.env.example` if missing
2. **Backend `.env`** — copied from `apps/backend/.env.sample` if missing

You can customize any variables in these files before or after bootstrapping.

### LM Studio Configuration

To use LM Studio with Resume Matcher, you need to configure the backend to use LM Studio's OpenAI-compatible API:

1. **Open LM Studio** and download a compatible model:
   - Recommended: `gemma-2-4b-it`, `phi-3.5-mini-instruct`, or similar lightweight models
   - Go to the "Search" tab, find your model, and download it

2. **Start the Local Server**:
   - Navigate to the "Local Server" tab in LM Studio
   - Select your downloaded model
   - Click "Start Server" (default port is `1234`)

3. **Configure Backend Environment Variables** in `apps/backend/.env`:
   ```env
   LLM_PROVIDER="llama_index.llms.openai_like.OpenAILike"
   LLM_BASE_URL="http://localhost:1234/v1"
   LL_MODEL="local-model"
   # LM Studio doesn't require an API key for local server
   LLM_API_KEY="not-needed"
   ```

4. **For embeddings**, you can use LM Studio if it supports embedding models, or use an alternative provider like OpenAI:
   ```env
   # Option 1: Use OpenAI for embeddings (requires API key)
   EMBEDDING_PROVIDER="openai"
   EMBEDDING_API_KEY="your-openai-api-key"
   EMBEDDING_MODEL="text-embedding-3-small"
   
   # Option 2: Use LM Studio for embeddings (if supported)
   EMBEDDING_PROVIDER="llama_index.embeddings.openai_like.OpenAILikeEmbedding"
   EMBEDDING_BASE_URL="http://localhost:1234/v1"
   EMBEDDING_MODEL="local-embedding-model"
   EMBEDDING_API_KEY="not-needed"
   ```

### Common Variables

| Name                      | Description                     | Default                        |
| ------------------------- | ------------------------------- | ------------------------------ |
| `SYNC_DATABASE_URL`       | Backend database connection URI | `sqlite:///db.sqlite3`         |
| `SESSION_SECRET_KEY`      | fastAPI session secret key      | `a-secret-key`                 |
| `PYTHONDONTWRITEBYTECODE` | Disable Python bytecode files   | `1`                            |
| `ASYNC_DATABASE_URL`      | Backend async db connection URI | `sqlite+aiosqlite:///./app.db` |
| `NEXT_PUBLIC_API_URL`     | Frontend proxy to backend URI   | `http://localhost:8000`        |
| `LLM_PROVIDER`            | LLM provider to use             | `llama_index.llms.openai_like.OpenAILike` (for LM Studio) |
| `LLM_BASE_URL`            | LM Studio API base URL          | `http://localhost:1234/v1`     |
| `LL_MODEL`                | Model name in LM Studio         | `local-model`                  |

> **Note:** `PYTHONDONTWRITEBYTECODE=1` is exported by `setup.sh` to prevent `.pyc` files.

---

## 📦 Installation Steps

 Note: Before You Run `setup.sh`
 
 Make sure that [LM Studio](https://lmstudio.ai/) is installed and you have:
 1. Downloaded a compatible model (e.g., `gemma-2-4b-it` or `phi-3.5-mini`)
 2. Started the local server in LM Studio (typically runs on `http://localhost:1234`)
 
 To start the LM Studio server:
 - Open LM Studio
 - Navigate to the "Local Server" tab
 - Click "Start Server"
 - Ensure the server is running before proceeding with setup
 
### Windows Installation

1. **Clone the repository**

   ```powershell
   git clone https://github.com/srbhr/Resume-Matcher.git
   cd Resume-Matcher
   ```

2. **Run PowerShell setup**

   ```powershell
   .\setup.ps1
   ```

   This will:

   - Verify/install prerequisites (`node`, `npm`, `python3`, `pip3`, `uv`)
   - Bootstrap root & backend `.env` files
   - Install Node.js deps (`npm ci`) at root and frontend
   - Sync Python deps in `apps/backend` via `uv sync`
   
   **Note:** The setup script no longer installs LM Studio automatically. You must download and install it manually from [https://lmstudio.ai/](https://lmstudio.ai/)

3. **(Optional) Start development**

   ```powershell
   .\setup.ps1 -StartDev
   ```

   Press `Ctrl+C` to gracefully shut down.

4. **Build for production**
   ```powershell
   npm run build
   ```

### Linux/macOS Installation

1. **Clone the repository**

   ```bash
   git clone https://github.com/srbhr/Resume-Matcher.git
   cd Resume-Matcher
   ```

2. **Make setup executable**

   ```bash
   chmod +x setup.sh
   ```

3. **Run setup**

   ```bash
   ./setup.sh
   ```

   This will:

   - Verify/install prerequisites (`node`, `npm`, `python3`, `pip3`, `uv`)
   - Bootstrap root & backend `.env` files
   - Install Node.js deps (`npm ci`) at root and frontend
   - Sync Python deps in `apps/backend` via `uv sync`
   
   **Note:** The setup script does not install LM Studio. You must download and install it manually from [https://lmstudio.ai/](https://lmstudio.ai/)

4. **(Optional) Start development**

   ```bash
   ./setup.sh --start-dev
   # or
   make setup
   make run-dev
   ```

   Press `Ctrl+C` to gracefully shut down.

5. **Build for production**
   ```bash
   npm run build
   # or
   make run-prod
   ```

---

## 🔨 Available Commands

### PowerShell Commands (Windows)

- **`.\setup.ps1`** — Run complete setup process
- **`.\setup.ps1 -StartDev`** — Setup and start development server
- **`.\setup.ps1 -Help`** — Show PowerShell script help
- **`npm run dev`** — Start development server
- **`npm run build`** — Build for production

### Makefile Targets (Linux/macOS)

- **`make help`** — Show available targets
- **`make setup`** — Run `setup.sh`
- **`make run-dev`** — Start dev server (SIGINT-safe)
- **`make run-prod`** — Build for production
- **`make clean`** — Remove build artifacts (customize as needed)

---

## 🐞 Troubleshooting

### Windows-specific Issues

- **`Execution of scripts is disabled on this system`**:

  - Run `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser` in PowerShell as Administrator.

- **`winget: command not found`**:

  - Install App Installer from Microsoft Store or update Windows 10/11.

- **`LM Studio not running`**:

  - Ensure LM Studio is installed from [https://lmstudio.ai/](https://lmstudio.ai/)
  - Start the local server in LM Studio before running the application
  - Verify the server is accessible at `http://localhost:1234` (default port)

- **`uv: command not found`** after installation:

  - Restart your PowerShell terminal and try again.

### Cross-platform Issues

- **`permission denied`** on `setup.sh`:

  - Run `chmod +x setup.sh`.

- **`uv: command not found`** despite install:

  - Ensure `~/.local/bin` is in your `$PATH`.

- **`Connection refused to LM Studio server`**:

  - Make sure LM Studio's local server is running (check the "Local Server" tab in LM Studio)
  - Verify the correct port is configured in your backend `.env` file (`LLM_BASE_URL`)

- **`npm ci` errors**:
  - Check your `package-lock.json` is in sync with `package.json`.

---

## 🖋️ Frontend

- Please make sure to have the format on save option enabled in your editor or run `npm run format` to format all staged changes.

_Last updated: May 25, 2025_
