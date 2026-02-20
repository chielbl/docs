
# 🤖 Claude Code — Getting Started Guide

> **Who is this for?** Developers who are new to AI-assisted coding or using Claude Code for the first time. No prior AI experience needed.

---

## 📖 Table of Contents

1. [What is Claude Code?](#what-is-claude-code)
2. [Installation & First Run](#installation--first-run)
3. [Configuration](#configuration)
4. [Essential Commands](#essential-commands)
5. [How to Write Good Prompts](#how-to-write-good-prompts)
6. [CLAUDE.md — Your Project Brain](#claudemd--your-project-brain)
7. [MCP — Connect to External Data](#mcp--connect-to-external-data)
8. [Agents](#agents)
9. [Skills](#skills)
10. [Commands (Reusable Prompts)](#commands-reusable-prompts)
11. [Hooks](#hooks)
12. [Claude Code on GitHub (PR Reviews)](#claude-code-on-github-pr-reviews)
13. [Claude Code Cloud](#claude-code-cloud)
14. [Starting a New Project from Scratch](#starting-a-new-project-from-scratch)
15. [Best Practices](#best-practices)
16. [Troubleshooting](#troubleshooting)
17. [Resources](#resources)
18. [Step to follow](#step-to-follow) 

---

## What is Claude Code?

Claude Code is an **AI coding assistant that runs in your terminal**. It understands your project, writes code, edits files, runs commands, and helps you build faster — all through natural language.

Think of it as a senior developer sitting next to you:
- You describe what you want → Claude does it
- It reads your files, understands context, and makes changes directly

> 📚 Official docs: https://docs.anthropic.com/en/docs/claude-code

---

## Installation & First Run

```bash
# Install globally via npm
npm install -g @anthropic-ai/claude-code

# Start Claude Code in your project folder
cd your-project
claude
```

On first run, you'll authenticate with your Anthropic account. After that, you're ready to go.

> 📚 Install guide: https://docs.anthropic.com/en/docs/claude-code/getting-started

---

## Configuration

Claude Code settings work at **three levels** — think of them like layers:

| Level | Scope | File location |
|-------|-------|---------------|
| **Global** | All your projects | `~/.claude/settings.json` |
| **Project** | One specific project | `your-project/.claude/settings.json` |
| **Local** | Current session only | `settings.local.json` |

### Quick Setup

```bash
# Start Claude Code
claude

# Then use the interactive config command
/config
```

Or edit the JSON files directly.

### Recommended Security Config

Paste this into your `.claude/settings.json` to block Claude from reading sensitive files:

```json
{
  "permissions": {
    "deny": [
      "Read(**/.env)",
      "Read(**/.env.*)",
      "Read(**/secrets/**)",
      "Read(**/config/**)",
      "Read(**/.git/**)",
      "Write(**/.env)",
      "Write(**/.env.*)",
      "Write(**/secrets/**)",
      "Write(**/config/**)",
      "Write(**/.git/**)"
    ]
  },
  "env": {
    "CLAUDE_CODE_HIDE_ACCOUNT_INFO": "1",
    "DISABLE_TELEMETRY": "1",
    "DISABLE_ERROR_REPORTING": "1"
  }
}
```

### Performance Config (Optional)

```json
{
  "env": {
    "CLAUDE_CODE_AUTO_COMPACT": "true",
    "MAX_CONTEXT_TOKENS": "8000"
  }
}
```

> 📚 Settings reference: https://docs.anthropic.com/en/docs/claude-code/settings

---

## Essential Commands

Type these **inside the Claude Code terminal session** (start with `/`):

| Command | What it does |
|---------|-------------|
| `/init` | Scans your project and creates/updates `CLAUDE.md` — **always run this first!** |
| `/clear` | Clears the current session context window |
| `/compact` | Summarizes history to reduce context size (run this if things slow down) |
| `/resume` | Shows and continues previous sessions |
| `/context` | Shows what context is currently being sent to Claude |
| `/usage` | View token usage for the active session |
| `/model` | Switch the AI model for the current session |
| `/mcp` | View and manage MCP servers |
| `/permissions` | View or change what Claude is allowed to do |
| `/agents` | Manage your agents |
| `/exit` | Exit Claude Code |
| `/rewind` | Undo the last suggested code change (or press `ESC` twice) |

### Keyboard Shortcut: Plan Mode

Press **`SHIFT + TAB`** to switch to command mode, then:
- `?` — Show all shortcuts
- Enable **Plan Mode** — Claude thinks and plans first, then implements (great for large tasks!)

> 📚 Command reference: https://docs.anthropic.com/en/docs/claude-code/cli-reference

---

## How to Write Good Prompts

**The better your prompt, the better the result.** Be specific and outcome-focused.

| ✅ Good prompt | ❌ Bad prompt |
|---|---|
| "Create a TypeScript React hook for debounced search input with 300ms delay" | "Make search work better" |
| "Create a button component that triggers an alert saying 'Hello World'" | "Add a button" |
| "Fix the login form validation — email field should reject non-email formats" | "Fix the form" |

**Tips:**
- Include the tech stack (TypeScript, React, Next.js, etc.)
- Describe the expected behavior, not just the feature
- Reference files with `@filename` — e.g., "Update `@components/Button.tsx` to accept a `disabled` prop"

---

## CLAUDE.md — Your Project Brain

`CLAUDE.md` is a special file at your project root. Claude Code **always reads this file first** to understand your project.

### Create it automatically

```bash
# Inside Claude Code session:
/init
```

This scans your project and generates a `CLAUDE.md` for you.

### What to add at the top

```markdown
# Project Overview

We're building the app described in @SPECS.md. Read that file for general 
architecture, database structure, tech stack, and application decisions.

## Communication Style
Keep replies concise. Focus on key information. No unnecessary fluff.

## Rules
- Always look up official documentation before using third-party libraries.
- Use the DocsExplorer agent for documentation lookup.
- Follow the architecture in @SPECS.md before making structural decisions.

## Tech Stack
- Framework: Next.js 14 (App Router)
- Language: TypeScript
- Styling: Tailwind CSS
- Database: Prisma + PostgreSQL
```

### Advanced additions (after setup)

Once you've set up MCPs, Agents, Skills, and Commands, reference them here so Claude uses them automatically — without you having to ask each time.

---

## MCP — Connect to External Data

**MCP (Model Context Protocol)** servers let Claude access live data: documentation, databases, web content, APIs, and more.

### Install an MCP server

```bash
# Example: context7 — fetches up-to-date library documentation
claude mcp add --transport http context7 https://mcp.context7.com/mcp
```

### View installed MCP servers

```bash
# Inside Claude Code session:
/mcp
```

### Popular MCP servers

| MCP | Use case |
|-----|---------|
| `context7` | Fetch live documentation for any library |
| `playwright` | Browser automation and UI testing |
| `github` | Interact with GitHub PRs, issues, repos |
| `filesystem` | Read/write files beyond default scope |

> ⚠️ **Playwright is expensive in tokens** — use it intentionally for automated testing.

> 📚 Popular MCPs: https://docs.anthropic.com/en/docs/claude-code/mcp

---

## Agents

Agents are **specialized sub-assistants** focused on a specific task (e.g., documentation lookup, code review, API testing).

### Create an agent

```
project/.claude/agents/docs-explorer.md
```

Example content:

```markdown
# DocsExplorer Agent

You are a documentation specialist. When called, look up official documentation 
for any library or framework mentioned. Always use the latest version.
Use context7 MCP to fetch live docs when available.
```

### Add agents in Claude Code

```bash
# Inside Claude Code session:
/agents
```

> 💡 **Tip:** Add the agent to `CLAUDE.md` so Claude uses it automatically.

> 📚 Example agent: https://github.com/academind/claude-code-course-resources/blob/main/code-snapshots/finished-project/CLAUDE.md

---

## Skills

Skills give Claude **extra knowledge** about specific technologies or coding patterns. They can also run code (Python, JavaScript, etc.).

### Create a skill

```
project/.claude/skills/modern-nextjs/SKILL.md
project/.claude/skills/clean-typescript/SKILL.md
```

A skill file contains best practices, conventions, or knowledge Claude should use when working in that domain.

### Add skills in Claude Code

```bash
# Inside Claude Code session:
/skills
```

> 📚 Example skill files:
> - https://github.com/academind/claude-code-course-resources/blob/main/code-snapshots/finished-project/.claude/skills/modern-best-practice-nextjs/SKILL.md
> - https://github.com/academind/claude-code-course-resources/blob/main/code-snapshots/finished-project/.claude/skills/clean-typescript/SKILL.md

---

## Commands (Reusable Prompts)

Commands are **saved prompts** you can trigger anytime without retyping them. Great for repeated tasks like code review, security audit, or changelog generation.

### Create a command

```
project/.claude/commands/code-review.md
```

Example content:

```markdown
Review the code for: $ARGUMENTS

Check for:
- BUGS: Logic errors, edge cases, null handling
- SECURITY: Injection risks, exposed secrets, auth issues
- PERFORMANCE: N+1 queries, unnecessary re-renders, memory leaks

Be concise. List only real issues with file + line reference.
```

### Use a command

Inside Claude Code, type `/` and select from the list. Then pass arguments like `BUGS`, `SECURITY`, or `PERFORMANCE`.

> 📚 Example command: https://github.com/academind/claude-code-course-resources/blob/main/code-snapshots/finished-project/.claude/commands/code-review.md

---

## Hooks

Hooks **automatically trigger scripts** when Claude takes certain actions — like running a bash command or writing a file.

### Example: Block dangerous `rm` commands

Create the hook script:

```bash
# project/.claude/hooks/block-rm.sh
#!/bin/bash
if echo "$1" | grep -q "rm -rf"; then
  echo "Blocked: rm -rf is not allowed" >&2
  exit 1
fi
```

Add to `.claude/settings.json`:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/block-rm.sh"
          }
        ]
      }
    ]
  }
}
```

> 📚 Hooks docs: https://docs.anthropic.com/en/docs/claude-code/hooks

---

## Claude Code on GitHub (PR Reviews)

You can configure Claude to **automatically review Pull Requests** on GitHub using GitHub Actions.

### How it works

1. A PR is opened or updated
2. A GitHub Action triggers Claude Code
3. Claude reviews the diff, adds comments, and optionally approves or requests changes

### Setup steps

**Step 1:** Add your Anthropic API key to GitHub Secrets
```
Settings → Secrets → Actions → New repository secret
Name: ANTHROPIC_API_KEY
```

**Step 2:** Create a GitHub Actions workflow file:

```yaml
# .github/workflows/claude-review.yml
name: Claude Code Review

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run Claude Code Review
        uses: anthropics/claude-code-action@beta
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
```

**Step 3:** Claude will now comment on every PR with:
- Bugs and logic issues
- Security concerns
- Code style suggestions
- Improvement ideas

> 📚 Claude Code GitHub Action: https://github.com/anthropics/claude-code-action
> 📚 Claude Code Base Action: https://github.com/anthropics/claude-code-base-action

---

## Claude Code Cloud

**Claude Code Cloud** lets you run Claude Code remotely — no local terminal needed. It's useful for:
- Running Claude on a remote server or CI environment
- Team members without local setup
- Long-running background tasks

### How to use it

1. Go to [claude.ai/code](https://claude.ai/code)
2. Log in with your Anthropic account
3. Connect your repository (GitHub supported)
4. Start a session — Claude will run in the cloud with access to your repo

### Key differences from local Claude Code

| Feature | Local | Cloud |
|---------|-------|-------|
| Setup | Terminal install | Browser-based |
| File access | Your machine | Connected repo |
| Team sharing | Manual | Built-in |
| CI integration | Via Actions | Native |

> 📚 More info: https://docs.anthropic.com/en/docs/claude-code

---

## Starting a New Project from Scratch

Follow this workflow for the best results:

### Step 1: Write a project spec

Write a document describing what you're building. Be as detailed as possible:
- What the app does
- Who uses it
- Key features
- Tech stack preferences

> 💡 Use ChatGPT or Claude.ai to refine your spec into a well-structured document.

### Step 2: Create SPECS.md

Save your spec as `SPECS.md` in your project root.

### Step 3: Start Claude Code with your spec

```
We're building an app described in @SPECS.md.
Please format the file as proper markdown and suggest a tech stack.
Then scaffold the initial project structure.
```

### Step 4: Use Plan Mode for large tasks

Press `SHIFT + TAB` → enable **Plan Mode**. Claude will think before acting — essential for complex or multi-file tasks.

### Step 5: Run /init

```bash
/init
```

This generates your `CLAUDE.md` from the project structure.

---

## Best Practices

### 🚀 Getting Started (do these first)

1. Run `/init` immediately after entering a project
2. Configure security permissions in `.claude/settings.json`
3. Add key info to `CLAUDE.md` (tech stack, architecture, rules)
4. Use `/compact` regularly on large codebases to stay fast

### ✍️ Prompting

- Be specific: include tech stack, file names, expected behavior
- Reference files with `@filename`
- Use **Plan Mode** for new features or complex refactors
- Break big tasks into smaller prompts

### 🔒 Security Checklist

- ✅ Deny `.env` and `.env.*` read/write access
- ✅ Block `secrets/` directories
- ✅ Disable telemetry if preferred
- ✅ Review `/context` before sensitive work
- ✅ Never commit `.claude/settings.local.json`

### 🧠 Advanced Setup

Once basics work, layer in:
1. **MCP** — connect to docs, APIs, databases
2. **Agents** — specialized sub-assistants for repeated tasks
3. **Skills** — extra knowledge for your tech stack
4. **Commands** — reusable prompt templates
5. **Hooks** — automated guards and scripts

---

## Troubleshooting

| Problem | Solution |
|---------|----------|
| "Context too large" error | Run `/compact` to summarize history |
| Claude doesn't know your project | Run `/init` to generate `CLAUDE.md` |
| Permission denied on file | Check the `deny` list in `settings.json` |
| Slow or poor responses | Use `/compact` + write more specific prompts |
| Wrong library version used | Add `context7` MCP for live docs |
| Need to undo a change | Type `/rewind` or press `ESC` twice |

---

## Resources

| Resource | Link |
|----------|------|
| Official Claude Code Docs | https://docs.anthropic.com/en/docs/claude-code |
| Settings Reference | https://docs.anthropic.com/en/docs/claude-code/settings |
| CLI Reference | https://docs.anthropic.com/en/docs/claude-code/cli-reference |
| MCP Servers | https://docs.anthropic.com/en/docs/claude-code/mcp |
| Hooks Docs | https://docs.anthropic.com/en/docs/claude-code/hooks |
| GitHub Action | https://github.com/anthropics/claude-code-action |
| Prompt Engineering | https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview |
| Example Project (Academind) | https://github.com/academind/claude-code-course-resources |

---

> 💡 **Pro Tip:** Keep this file as `CLAUDE.md` or share it with your team as a quick-start reference. Pin it to your repo wiki or README for easy access.

## Step to follow 

### 1) Idea → “Prompt request”
Write your product idea in ChatGPT and ask it to produce a single, high-quality build prompt.

Include (copy/paste structure):

- Goal: what you’re building + who it’s for.
- Features: user stories (list them as bullets).
- Data/API: what backend/source (e.g., GitHub REST), auth method, env vars.
- Tech stack: strict requirements (Next.js App Router, Server Components, TS, CSS Modules, etc.).
- UI/UX: style, layout, accessibility, responsive expectations.

Output you want from ChatGPT: one “master prompt” you can reuse in other tools or chats.

```
Write me a prompt, where I will build a forum web application.
The app is in de background connected with Github by using a github token where I can fetch with data.
the forum see all the issues fromt the repo, you can search on title, as user i can create a new issue after fillen a form with title, description, label have 2 options = "bug" or "suggestion".
Once you click on the issue you wil land on a detail page to see the information about the issue.

UX/UI:
I want a very modern look, that make the user easy and clear to use.
I want to use clear and bright colors.

TECH:
Moder Next.js with focus on server side components, styling will me css-modules, typescript, for icons use lucide-react, github
```

### 2) Prompt → GUIDELINES.md (technical documentation)
Open a new chat and paste parts of the master prompt, then ask for “technical documentation / guideline” that you can commit to the repo.

```
Write me a technical documentation / guideline based on this prompt that you can commit to the repo.:
Write me a prompt, where I will build a forum web application. The app is in de background connected with Github by using a github token where I can fetch with data. the forum see all the issues fromt the repo, you can search on title, as user i can create a new issue after fillen a form with title, description, label have 2 options = "bug" or "suggestion". Once you click on the issue you wil land on a detail page to see the information about the issue.

The app must:
- Fetch and display all issues from a specific GitHub repository
- Allow searching issues by title
- Allow users to create a new issue via a form
- Display issue details on a separate dynamic page

# TECH STACK (STRICT REQUIREMENTS)
- Next.js (latest version, App Router)
- Focus on Server Components (use Client Components only when necessary)
- TypeScript
- CSS Modules for styling
- lucide-react for icons
- GitHub REST API
- Use environment variables for the GitHub token

# UI/UX REQUIREMENTS
Design must:
- Have a modern, clean, minimal look
- Use bright and clear colors
- Have strong visual hierarchy
- Be easy to use and intuitive
- Use card-based layout for issues
- Use subtle hover effects
- Be responsive (mobile-friendly)

Use CSS Modules only (no Tailwind, no styled-components).

# ARCHITECTURE REQUIREMENTS
- Use Server Components by default
- Create a separate GitHub API utility layer
- Use environment variables for:
GITHUB_TOKEN
GITHUB_OWNER
GITHUB_REPO
- Keep clean folder structure
- Add reusable components
- Follow best practices
```

### 3) (Optional) Generate UI in Google Stitch
Use the app description + UI/UX requirements as the Stitch prompt to generate screens and a prototype, then iterate by giving targeted edit instructions (colors, hierarchy, component patterns).

### 4) Add GUIDELINES.md to your repo
Create GUIDELINES.md at project root and paste the final guideline from step (2).

### 5) Generate CLAUDE.md via /init, then link to guidelines
Run Claude Code’s /init inside the repo; it will analyze the codebase and generate a starter CLAUDE.md (commands, directories, conventions it detects).
​
Then add this line near the top (or in a “Project context” section):
```
We’re building the app described in @GUIDELINES.md. Read that file for general architecture, tech stack, and application decisions.

Keep your replies extremely concise and focus on conveying the key information. No unnecessary fluff, no long code snippets.

Whenever working with any third-party library or something similar, you MUST look up the official documentation to ensure that you're working with up-to-date information.
```
---
*Last updated: February 2026*
