---
name: vscode-builder
description: "Build and test applications in VS Code with automatic dependency installation. Use VS Code CLI, GitHub Copilot, tasks, debugging, and browser testing. Scaffolds projects, installs deps, runs dev servers, and validates apps."
metadata: {"openclaw":{"emoji":"💻","requires":{"anyBins":["code","code-insiders"]}}}
---

# VS Code Builder

Build, run, and test applications using VS Code CLI and GitHub Copilot. **Automatically installs dependencies** when creating or opening projects.

## Auto-Install Dependencies

**IMPORTANT:** Always install dependencies automatically when:
1. Creating a new project
2. Opening an existing project with `package.json`
3. Cloning a repository

### Dependency Installation Flow

```bash
# Check if package.json exists and install deps
if [ -f "package.json" ]; then
  # Detect package manager
  if [ -f "pnpm-lock.yaml" ]; then
    pnpm install
  elif [ -f "yarn.lock" ]; then
    yarn install
  elif [ -f "bun.lockb" ]; then
    bun install
  else
    npm install
  fi
fi
```

### Package Manager Detection

| Lock File | Package Manager | Install Command |
|-----------|-----------------|-----------------|
| `pnpm-lock.yaml` | pnpm | `pnpm install` |
| `yarn.lock` | yarn | `yarn install` |
| `bun.lockb` | bun | `bun install` |
| `package-lock.json` | npm | `npm install` |
| (none) | npm (default) | `npm install` |

## VS Code CLI

```bash
# Open folder in VS Code
code ~/Projects/myapp

# Open file at specific line
code ~/Projects/myapp/src/index.ts:42

# Open in new window
code -n ~/Projects/myapp

# Diff two files
code --diff file1.ts file2.ts

# List installed extensions
code --list-extensions

# Install extension
code --install-extension GitHub.copilot
```

## Project Scaffolding (with Auto-Install)

### React + TypeScript (Vite)

```bash
# Create, install deps, and open
cd ~/Projects
npm create vite@latest myapp -- --template react-ts
cd myapp
npm install
code .
```

### Next.js

```bash
cd ~/Projects
npx create-next-app@latest myapp --typescript --tailwind --eslint --app
cd myapp
# Dependencies installed automatically by create-next-app
code .
```

### React Native

```bash
cd ~/Projects
npx @react-native-community/cli init MyApp --template react-native-template-typescript
cd MyApp
# Dependencies installed automatically
code .
```

### Node.js Project

```bash
mkdir -p ~/Projects/myapp && cd ~/Projects/myapp
npm init -y
npm install express typescript @types/node @types/express ts-node --save
npm install --save-dev nodemon
code .
```

### Python Project

```bash
mkdir -p ~/Projects/myapp && cd ~/Projects/myapp
python3 -m venv venv
source venv/bin/activate
pip install flask requests
pip freeze > requirements.txt
code .
```

### Clone and Install

```bash
cd ~/Projects
git clone https://github.com/user/repo.git myapp
cd myapp

# Auto-detect and install
if [ -f "pnpm-lock.yaml" ]; then pnpm install
elif [ -f "yarn.lock" ]; then yarn install
elif [ -f "bun.lockb" ]; then bun install
elif [ -f "package.json" ]; then npm install
elif [ -f "requirements.txt" ]; then pip install -r requirements.txt
elif [ -f "Gemfile" ]; then bundle install
elif [ -f "go.mod" ]; then go mod download
fi

code .
```

## One-Line Project Creators

```bash
# Vite + React + TS (complete)
cd ~/Projects && npm create vite@latest myapp -- --template react-ts && cd myapp && npm install && code .

# Next.js (complete)
cd ~/Projects && npx create-next-app@latest myapp --typescript --tailwind --eslint --app && cd myapp && code .

# Express + TS (complete)
mkdir -p ~/Projects/myapp && cd ~/Projects/myapp && npm init -y && npm i express typescript ts-node @types/node @types/express && npx tsc --init && code .

# Astro (complete)
cd ~/Projects && npm create astro@latest myapp -- --template minimal --typescript strict && cd myapp && npm install && code .
```

## GitHub Copilot Integration

### Copilot CLI

```bash
# Get command suggestions
gh copilot suggest "find large files in git history"

# Explain a command
gh copilot explain "git rebase -i HEAD~5"
```

### Project-Specific Copilot Instructions

Create `.github/copilot-instructions.md` in project root:

```markdown
# Copilot Instructions
- TypeScript React app with Tailwind CSS
- Use functional components with hooks
- API calls via src/api/client.ts
```

### Copilot Chat Commands (in VS Code)

- `/explain` - Explain code
- `/fix` - Fix problems
- `/tests` - Generate tests
- `@workspace` - Ask about codebase

## Dev Server + Browser Testing

### Start Dev Server (Background)

```bash
# Start dev server in background, get sessionId for monitoring
bash background:true workdir:~/Projects/myapp command:"npm run dev"

# Monitor server output
process action:log sessionId:XXX

# Check if still running
process action:poll sessionId:XXX
```

### Browser Testing with OpenClaw

Once dev server is running, use the `browser` tool to test the application:

```bash
# Open app in browser
browser action:navigate url:"http://localhost:5173"

# Take screenshot to verify UI
browser action:screenshot

# Check for specific element
browser action:evaluate script:"document.querySelector('h1')?.textContent"

# Click a button
browser action:click selector:"button.submit"

# Fill a form field
browser action:type selector:"input[name='email']" text:"test@example.com"

# Get page title
browser action:evaluate script:"document.title"

# Check for errors in console
browser action:evaluate script:"window.__errors || []"
```

### Full Test Flow Example

1. **Start dev server:**
```bash
bash background:true workdir:~/Projects/myapp command:"npm run dev"
# Returns sessionId: abc123
```

2. **Wait for server ready:**
```bash
process action:log sessionId:abc123
# Look for "Local: http://localhost:5173"
```

3. **Open in browser:**
```bash
browser action:navigate url:"http://localhost:5173"
```

4. **Verify page loads:**
```bash
browser action:screenshot
browser action:evaluate script:"document.title"
```

5. **Test interactions:**
```bash
browser action:click selector:"#login-btn"
browser action:type selector:"#email" text:"user@test.com"
browser action:type selector:"#password" text:"password123"
browser action:click selector:"button[type='submit']"
```

6. **Verify result:**
```bash
browser action:screenshot
browser action:evaluate script:"document.querySelector('.welcome-message')?.textContent"
```

7. **Stop server when done:**
```bash
process action:kill sessionId:abc123
```

## Tasks Configuration

Create `.vscode/tasks.json`:

```bash
mkdir -p .vscode && cat > .vscode/tasks.json << 'EOF'
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "dev",
      "type": "npm",
      "script": "dev",
      "isBackground": true,
      "problemMatcher": []
    },
    {
      "label": "build",
      "type": "npm",
      "script": "build",
      "group": { "kind": "build", "isDefault": true }
    },
    {
      "label": "test",
      "type": "npm",
      "script": "test",
      "group": { "kind": "test", "isDefault": true }
    }
  ]
}
EOF
```

## Debug Configuration

Create `.vscode/launch.json`:

```bash
mkdir -p .vscode && cat > .vscode/launch.json << 'EOF'
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "chrome",
      "request": "launch",
      "name": "Debug in Chrome",
      "url": "http://localhost:5173",
      "webRoot": "${workspaceFolder}"
    },
    {
      "type": "node",
      "request": "launch",
      "name": "Debug Tests",
      "program": "${workspaceFolder}/node_modules/.bin/vitest",
      "args": ["--run"],
      "console": "integratedTerminal"
    }
  ]
}
EOF
```

## Workspace Settings

Create `.vscode/settings.json`:

```bash
mkdir -p .vscode && cat > .vscode/settings.json << 'EOF'
{
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit"
  },
  "github.copilot.enable": {
    "*": true
  }
}
EOF
```

## Error Handling & Debugging

### Common Errors & Solutions

**Directory not found:**
```bash
# Always verify directory exists before changing to it
bash command:"ls -la ~/Projects"
bash command:"cd ~/Projects/myapp && pwd"
```

**Dev server issues:**
```bash
# Check if server is actually running
process action:log sessionId:XXX
# Look for "Local: http://localhost:XXXX" in output

# If port already in use, find and kill process
bash command:"lsof -ti:5173 | xargs kill -9"
```

**Browser connection failed:**
```
Error: Chrome extension relay is running, but no tab is connected
```
**Solution:** Open Chrome and click the OpenClaw extension icon on any tab first.

### Error Logging Pattern

Always log errors with context:

```bash
# When a command fails, capture the error
bash command:"npm create vite@latest myapp -- --template react-ts 2>&1"
# Check exit code and provide context
bash command:"echo 'Exit code: $?'"

# For browser operations, check status first
browser action:evaluate script:"document.readyState"
# Then proceed with operations
```

### Project Creation Verification

```bash
# Verify project was created successfully
bash command:"ls -la ~/Projects/myapp"
bash command:"cat ~/Projects/myapp/package.json | head -10"

# Verify dependencies installed
bash command:"cd ~/Projects/myapp && npm list --depth=0"
```

### Dev Server Health Check

```bash
# Start server with error capture
bash background:true workdir:~/Projects/myapp command:"npm run dev 2>&1"
# Wait and check for successful startup
sleep 5
process action:log sessionId:XXX

# Test if port is responding
bash command:"curl -s -o /dev/null -w '%{http_code}' http://localhost:5173"
# Should return 200 if working
```

### Browser Testing Prerequisites

1. **Verify Chrome extension connection:**
```bash
# This will fail if extension not connected - show user clear message
browser action:evaluate script:"navigator.userAgent"
```

2. **Check if dev server is ready:**
```bash
# Test server response before browser navigation
bash command:"curl -I http://localhost:5173"
```

### Compact Error Reporting

When errors occur, provide:
- **Error type**: Connection/Directory/Command failure
- **Root cause**: Missing extension/wrong path/port in use
- **Next steps**: Specific action to fix

Example error message format:
```
❌ Error: Project creation failed
🔍 Cause: Directory ~/Projects does not exist
🔧 Fix: Run `mkdir -p ~/Projects` first
```

## Tips

1. **Always install dependencies** - Run install command after project creation
2. **Detect package manager** - Check for lock files before installing
3. **Verify prerequisites** before executing commands
4. **Use error capture** (`2>&1`) for better debugging
5. **Check exit codes** and provide context when commands fail
6. **Test connectivity** before browser operations
7. **Log intermediate steps** for complex workflows
8. **Provide actionable error messages** with specific fix instructions
9. **Verify each step** before proceeding to the next

## Auto-Install Checklist

Before running any project, always check:

```bash
# 1. Check if node_modules exists
ls node_modules 2>/dev/null || echo "Need to install dependencies"

# 2. Auto-install based on lock file
install_deps() {
  if [ ! -d "node_modules" ]; then
    echo "📦 Installing dependencies..."
    if [ -f "pnpm-lock.yaml" ]; then pnpm install
    elif [ -f "yarn.lock" ]; then yarn install  
    elif [ -f "bun.lockb" ]; then bun install
    elif [ -f "package.json" ]; then npm install
    fi
    echo "✅ Dependencies installed"
  fi
}

# 3. For Python projects
if [ -f "requirements.txt" ] && [ ! -d "venv" ]; then
  python3 -m venv venv
  source venv/bin/activate
  pip install -r requirements.txt
fi
```

## Quick Reference

| Action | Command |
|--------|---------|
| Create React+TS | `npm create vite@latest app -- --template react-ts && cd app && npm i && code .` |
| Create Next.js | `npx create-next-app@latest app --ts --tailwind && cd app && code .` |
| Install deps (npm) | `npm install` |
| Install deps (pnpm) | `pnpm install` |
| Install deps (yarn) | `yarn install` |
| Install deps (bun) | `bun install` |
| Run dev server | `npm run dev` |
| Build project | `npm run build` |
| Open in VS Code | `code .` |
