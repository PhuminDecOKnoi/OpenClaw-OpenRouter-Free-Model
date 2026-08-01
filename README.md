# OpenClaw + OpenRouter Free Model

![Documentation](https://img.shields.io/badge/type-documentation-blue)
![OpenClaw](https://img.shields.io/badge/platform-OpenClaw-purple)
![OpenRouter](https://img.shields.io/badge/provider-OpenRouter-orange)
![Language](https://img.shields.io/badge/language-English%20US-lightgrey)
![Security](https://img.shields.io/badge/security-first-critical)

> A practical English (US) guide for installing, configuring, teaching, and operating **OpenClaw** with **OpenRouter Free Models**. This repository focuses on real-world usage, cost control, API-key safety, classroom workshops, and AI-agent automation.

---

## Table of Contents

- [Overview](#overview)
- [Who This Repository Is For](#who-this-repository-is-for)
- [Key Concepts](#key-concepts)
- [Architecture](#architecture)
- [Repository Scope](#repository-scope)
- [Repository Files](#repository-files)
- [Requirements](#requirements)
- [Quick Start](#quick-start)
- [Installation](#installation)
- [Dashboard and Gateway](#dashboard-and-gateway)
- [OpenRouter Setup](#openrouter-setup)
- [Model Strategy](#model-strategy)
- [Free Model Operating Rules](#free-model-operating-rules)
- [Web Search](#web-search)
- [Cron Automation](#cron-automation)
- [Telegram Recovery](#telegram-recovery)
- [File Workflow](#file-workflow)
- [Prompt Pattern](#prompt-pattern)
- [Security and Token Hygiene](#security-and-token-hygiene)
- [Troubleshooting](#troubleshooting)
- [Workshop Mode](#workshop-mode)
- [Classroom Lab Ideas](#classroom-lab-ideas)
- [Command Cheat Sheet](#command-cheat-sheet)
- [Recommended Repository Structure](#recommended-repository-structure)
- [Maintenance Notes](#maintenance-notes)
- [External References](#external-references)
- [License](#license)

---

## Overview

**OpenClaw** is an AI-agent gateway that connects users, communication channels, model providers, and tools. It can be used with providers such as OpenRouter, OpenAI, Anthropic, Gemini, and local model endpoints.

This repository provides a practical operating guide for using OpenClaw with **OpenRouter Free Models**. Its main goals are to help users:

1. Install and verify OpenClaw.
2. Connect OpenRouter safely.
3. Use free or cost-sensitive models with lower billing risk.
4. Build workflows for Telegram, Dashboard, Web Search, Cron, and local files.
5. Teach OpenClaw concepts in IT, AI-agent, automation, or digital-business courses.

---

## Who This Repository Is For

| Audience | Use Case |
|---|---|
| New AI-agent users | Install OpenClaw and test OpenRouter models. |
| IT instructors and trainers | Run a step-by-step workshop. |
| IT / CS / Software Engineering students | Understand AI-agent architecture and model routing. |
| Developers and automation users | Configure Cron, Telegram, Web Search, and file workflows. |
| Knowledge workers | Use an AI agent to summarize, classify, and organize information. |

---

## Key Concepts

| Term | Meaning |
|---|---|
| **OpenClaw Gateway** | The local gateway that receives user requests and routes them to agents, models, and tools. |
| **Dashboard** | A local web interface for checking and controlling OpenClaw. |
| **Model Provider** | A model service such as OpenRouter, OpenAI, Anthropic, Gemini, or a local endpoint. |
| **Model Ref** | A provider-qualified model reference, such as `openrouter/<provider>/<model-id>`. |
| **Agent Session** | A conversation or task session handled by the AI agent. |
| **Tool** | An added capability such as Web Search, File Search, Cron, or local file access. |
| **Cron Automation** | A scheduled task, such as a daily brief or monitoring workflow. |
| **Free Model** | A model that OpenRouter currently marks as free or exposes with a `:free` suffix. |

---

## Architecture

```text
User / Student / Trainer
        ↓
Telegram / Dashboard / CLI
        ↓
OpenClaw Gateway
        ↓
Agent Session
        ↓
Model Provider Layer
- OpenRouter
- OpenAI
- Anthropic
- Gemini
- Local endpoint
        ↓
Tool Layer
- Web Search
- Files
- Cron
- Logs
- External APIs
        ↓
Output
- Summary
- Report
- Classification
- Draft
- Teaching Demo
```

### Teaching Note

Explain to learners that OpenClaw is not the language model itself. It is an **agent gateway and orchestration layer** that connects users to model providers and tools.

---

## Repository Scope

This repository covers:

- OpenClaw installation baseline
- OpenRouter API-key setup
- Model configuration concepts
- Free-model usage patterns
- Web Search configuration
- Cron automation patterns
- Telegram recovery
- File workflow
- Security and token hygiene
- Troubleshooting
- Workshop checklists
- Command cheat sheets

This repository must **not** store:

- Real API keys
- Real Telegram bot tokens
- Real gateway tokens
- Passwords
- `.env` files containing secrets
- Customer files
- Personal data
- Confidential business data

---

## Repository Files

| File | Purpose |
|---|---|
| `README.md` | Main repository landing page and quick operating guide. |
| `openclaw_openrouter_free_model_installation_manual_th.md` | English (US) installation and operation manual. The filename is preserved for backward compatibility. |
| `openclaw_openai_gpt_5_x_lesson_th.md` | English (US) instructor lesson plan. The filename is preserved for backward compatibility. |

> Note: Some filenames still contain `_th` because earlier versions were written in Thai. The content has been converted to English (US). Rename files only after confirming that no external links depend on the current filenames.

---

## Requirements

| Component | Recommendation |
|---|---|
| Operating system | macOS first; Linux and WSL2 can be adapted. |
| Node.js | Use an active LTS version or the version recommended by OpenClaw at installation time. |
| npm | Use the npm version bundled with Node.js. |
| Browser | Chrome, Edge, or Safari for the Dashboard. |
| OpenRouter account | Required to create and manage API keys. |
| Password manager | Strongly recommended for storing API keys. |

### Pre-check

```bash
sw_vers
node --version
npm --version
which node
which npm
```

If Node.js is not installed on macOS:

```bash
brew install node
```

---

## Quick Start

Use this flow for a short workshop demo:

```bash
# 1) Install OpenClaw
npm install -g openclaw@latest

# 2) Start onboarding
openclaw onboard --install-daemon

# 3) Check system status
openclaw --version
openclaw doctor
openclaw gateway status

# 4) Open the Dashboard
openclaw dashboard
```

Then connect OpenRouter and test model status:

```bash
openclaw models auth login --provider openrouter
openclaw models status
openclaw models status --probe
```

---

## Installation

### Option A: Installer Script

```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

### Option B: npm

```bash
npm install -g openclaw@latest
openclaw onboard --install-daemon
```

### Verify Installation

```bash
openclaw --version
openclaw doctor
openclaw gateway status
```

Expected result:

```text
Gateway: running
Dashboard: http://127.0.0.1:18789/
Connectivity probe: ok
```

If the result is different, see [Troubleshooting](#troubleshooting).

---

## Dashboard and Gateway

### Open the Dashboard

```bash
openclaw dashboard
```

Or open the local URL directly:

```bash
open http://127.0.0.1:18789
```

### Restart the Gateway

```bash
openclaw gateway restart
```

Avoid unsupported shortcuts such as `openclaw restart`. Use `openclaw gateway restart` instead.

### Health Check

```bash
openclaw gateway status
openclaw models status
openclaw models status --probe
openclaw cron list
```

---

## OpenRouter Setup

### 1. Create an OpenRouter API Key

1. Sign in to OpenRouter.
2. Go to **API Keys**.
3. Create a new key.
4. Name it clearly, for example `OpenClaw-Teaching-Free-Model`.
5. Copy the key and store it in a password manager.

Common key format:

```text
sk-or-v1-...
```

### 2. Connect OpenClaw to OpenRouter

Use OpenClaw model authentication:

```bash
openclaw models auth login --provider openrouter
```

Alternative onboarding flow:

```bash
export OPENROUTER_API_KEY="<your-openrouter-api-key>"
openclaw onboard --auth-choice apiKey --token-provider openrouter --token "$OPENROUTER_API_KEY"
```

### 3. Verify Provider Status

```bash
openclaw models auth list
openclaw models status
openclaw models status --probe
```

---

## Model Strategy

### Recommended Principle

```text
Use exact model refs.
Avoid ambiguous model refs.
Verify free model status before a classroom demo.
Keep fallback models cost-safe.
```

### Model Ref Pattern

OpenClaw uses provider-qualified model references:

```text
provider/model
```

For OpenRouter models, use a fully qualified reference:

```text
openrouter/<provider>/<model-id>
openrouter/<provider>/<model-id>:free
```

If the OpenRouter model ID contains a slash, keep the provider prefix clear:

```text
openrouter/moonshotai/kimi-k2
```

### Find Available Models

```bash
openclaw models list --provider openrouter
openclaw models scan
```

### Set the Primary Model

Choose a model from the current list before using it:

```bash
openclaw models set "openrouter/<provider>/<model-id>:free"
```

### Set a Fallback

```bash
openclaw models fallbacks clear
openclaw models fallbacks add "openrouter/<provider>/<fallback-model-id>:free"
```

### Add an Alias

```bash
openclaw models aliases add or-free "openrouter/<provider>/<model-id>:free"
openclaw models aliases list
```

### Restart and Probe

```bash
openclaw gateway restart
openclaw models status --probe
```

### Classroom Safe Default

```text
Primary model  = verified free model
Fallback model = verified free model
Output limit   = short
Tool calls     = minimal
PDF reading    = avoid full-document reading
Cron frequency = low
```

---

## Free Model Operating Rules

Free-model availability, rate limits, latency, context size, and tool support can change. Verify the model list before teaching or running a public demo.

Recommended rules:

1. Check the catalog before the demo.
2. Avoid `auto` if you require strict free-only behavior.
3. Use exact model refs when teaching.
4. Limit prompt size and output length.
5. Avoid asking the agent to read large files in full.
6. Do not run Cron too frequently.
7. Check logs after the demo.

### Demo Prompt

```text
Answer in English in no more than five bullet points.
Explain how OpenClaw and OpenRouter work together.
Do not call external tools.
```

---

## Web Search

If you see this error:

```text
web_search is disabled or no provider is available
```

Configure a web provider:

```bash
openclaw configure --section web
openclaw gateway restart
```

| Provider | Strength | Best For |
|---|---|---|
| DuckDuckGo | Quick testing without an API key | Classroom demos |
| Brave | API-based and more stable | Production-like workflows |
| Gemini Search | Grounding and citations | Research workflows |
| SearXNG | Self-hosted and privacy-oriented | Advanced users |

Teaching point: ask students to distinguish between model memory, web-augmented answers, and cited/grounded answers.

---

## Cron Automation

Cron is useful for repeated work such as daily briefs, reminders, monitoring, and summaries.

### Cost-Safe Cron Design

```text
Limit result count.
Limit answer length.
Use isolated sessions.
Use cost-safe models.
Avoid full-file reading.
Avoid long chains of tool calls.
```

### Example: Daily Lightweight Brief

```bash
MSG=$(cat <<'EOF'
Create a lightweight daily brief.

Search only for important items from the last seven days.
Limit the result to three items.
Answer in English.
Keep the response under 700 words.
Do not read full PDFs.
Do not produce a long legal or technical analysis.
If no important item is found, say: "No major item met the criteria today."

Format:
1) Overall status
2) Key items
3) Short impact notes
4) Sources, if available
EOF
)

openclaw cron add \
  --name "daily-lightweight-brief" \
  --cron "0 8 * * *" \
  --tz "Asia/Bangkok" \
  --session isolated \
  --announce \
  --channel telegram \
  --to "<telegram-chat-id>" \
  --model "openrouter/<provider>/<model-id>:free" \
  --message "$MSG"
```

---

## Telegram Recovery

If a Telegram session is stuck, reset the session:

```text
/new
```

Then test with a short message:

```text
Check the system status briefly.
```

If problems continue, check:

```bash
openclaw gateway status
openclaw models status --probe
openclaw logs --follow
```

---

## File Workflow

Use this safe workflow:

```text
Read first → confirm path → back up before editing → write narrowly → verify result
```

### Example Workspace

```bash
mkdir -p "$HOME/AI-Agent-Lab/input"
mkdir -p "$HOME/AI-Agent-Lab/output"
```

### Read Files

```bash
cat "$HOME/AI-Agent-Lab/input/sample.txt"
head -80 "$HOME/AI-Agent-Lab/input/sample.txt"
```

### Write a Markdown File

```bash
cat <<'EOF' > "$HOME/AI-Agent-Lab/output/summary.md"
# Summary

This is a sample summary.
EOF
```

### Back Up Before Editing

```bash
cp "$HOME/AI-Agent-Lab/output/summary.md" \
   "$HOME/AI-Agent-Lab/output/summary.backup.$(date +%Y%m%d-%H%M%S).md"
```

---

## Prompt Pattern

Use a structured prompt:

```text
Role:
You are ...

Task:
What to do.

Input:
The data or source to use.

Constraints:
Length, style, forbidden actions, required sources.

Output format:
Headings, table, JSON, or bullet points.
```

Example:

```text
Summarize the meeting notes below.
- Answer in English.
- Keep the answer under 500 words.
- Separate decisions from action items.
- If the information is insufficient, state that clearly.
```

---

## Security and Token Hygiene

Never expose:

```text
API keys
Gateway tokens
Telegram bot tokens
Passwords
Session tokens
.env files
auth profiles
```

### Recommended Practice

- Store keys in a password manager.
- Use environment variables only when needed.
- Do not paste secrets into public chat or GitHub files.
- Rotate a key immediately if it may have been exposed.
- Review logs before sharing them.
- Back up configuration files before making changes.

### Configuration Backup

```bash
cp "$HOME/.openclaw/openclaw.json" \
   "$HOME/.openclaw/openclaw.backup.$(date +%Y%m%d-%H%M%S).json"
```

---

## Troubleshooting

| Symptom | Likely Cause | Recommended Action |
|---|---|---|
| `401` or missing authentication | API key not configured or not loaded | Re-authenticate the provider and restart the gateway. |
| Billing or credit error | Paid model or insufficient credits | Switch to a verified free model and reduce token usage. |
| `Unknown model` | Invalid or stale model ref | Run `openclaw models list --provider openrouter` and update the model ref. |
| `web_search disabled` | Web provider not configured | Run `openclaw configure --section web`. |
| Context overflow | Prompt, files, and history are too large | Use `/new`, reduce context, and split the task. |
| Cron repeats too often | Schedule is too aggressive | Lower frequency and reduce output length. |
| Telegram does not respond | Stuck session or gateway issue | Send `/new`, then check gateway and logs. |

---

## Workshop Mode

Suggested workshop flow:

| Time | Activity |
|---|---|
| 0:00–0:20 | Explain AI-agent architecture. |
| 0:20–0:45 | Install OpenClaw and check the gateway. |
| 0:45–1:10 | Connect OpenRouter and verify the model. |
| 1:10–1:40 | Run a Dashboard or Telegram demo. |
| 1:40–2:10 | Configure a lightweight Cron task. |
| 2:10–2:40 | Troubleshoot common errors. |
| 2:40–3:00 | Security review and Q&A. |

---

## Classroom Lab Ideas

1. Draw the OpenClaw architecture from memory.
2. Compare direct model calls with agent-gateway calls.
3. Identify safe and unsafe API-key practices.
4. Create a lightweight daily brief prompt.
5. Design a fallback strategy for free models.
6. Explain why full-file reading can increase cost and failure risk.

---

## Command Cheat Sheet

```bash
# System
openclaw --version
openclaw doctor
openclaw gateway status
openclaw gateway restart
openclaw dashboard

# Models
openclaw models auth login --provider openrouter
openclaw models auth list
openclaw models list --provider openrouter
openclaw models scan
openclaw models status
openclaw models status --probe
openclaw models set "openrouter/<provider>/<model-id>:free"
openclaw models fallbacks clear
openclaw models fallbacks add "openrouter/<provider>/<model-id>:free"
openclaw models aliases list

# Web Search
openclaw configure --section web
openclaw gateway restart

# Cron
openclaw cron list
openclaw cron run "<job-id>"
openclaw cron runs --id "<job-id>"
openclaw cron disable "<job-id>"
openclaw cron enable "<job-id>"

# Logs
openclaw logs --help
openclaw logs --follow
```

---

## Recommended Repository Structure

```text
.
├── README.md
├── openclaw_openrouter_free_model_installation_manual_th.md
├── openclaw_openai_gpt_5_x_lesson_th.md
├── LICENSE
└── docs/
    ├── assets/
    └── examples/
```

Future improvement: rename `_th` files to `_en_us` after confirming that existing links and references will not break.

---

## Maintenance Notes

Before running a workshop:

1. Check the latest OpenClaw documentation.
2. Check the latest OpenRouter model catalog.
3. Verify the free model selected for the demo.
4. Test `openclaw models status --probe`.
5. Prepare a backup model.
6. Confirm that no secrets are included in slides or repository files.
7. Keep prompts short and reproducible.

---

## External References

- OpenClaw documentation: https://docs.openclaw.ai/
- OpenClaw Models CLI: https://docs.openclaw.ai/cli/models
- OpenClaw model providers: https://docs.openclaw.ai/concepts/model-providers
- OpenRouter documentation: https://openrouter.ai/docs
- OpenRouter Quickstart: https://openrouter.ai/docs/quickstart
- OpenRouter model catalog: https://openrouter.ai/models
- OpenRouter Free Models Router: https://openrouter.ai/openrouter/free

---

## License

Use the repository license file as the source of truth. If this repository is intended to be freely reusable, keep the license explicit, for example MIT.

---

## Summary

This repository is a practical, security-first guide for using OpenClaw with OpenRouter Free Models. It is designed for installation, classroom teaching, workshop delivery, and controlled automation without exposing secrets or accidentally increasing model costs.
