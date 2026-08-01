<p align="center">
  <img src="assets/openclaw-openrouter-free-model-banner.svg" alt="OpenClaw + OpenRouter Free Model repository banner" width="100%">
</p>

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
- [Repository Files](#repository-files)
- [Quick Start](#quick-start)
- [Installation](#installation)
- [OpenRouter Setup](#openrouter-setup)
- [Model Strategy](#model-strategy)
- [Free Model Operating Rules](#free-model-operating-rules)
- [Security and Token Hygiene](#security-and-token-hygiene)
- [Troubleshooting](#troubleshooting)
- [Workshop Mode](#workshop-mode)
- [External References](#external-references)
- [License](#license)

---

## Overview

**OpenClaw** is an AI-agent gateway and orchestration layer. It connects users, channels, model providers, and tools so an AI workflow can operate through a Dashboard, CLI, Telegram, Cron automation, local files, and external providers.

This repository provides a practical operating guide for using OpenClaw with **OpenRouter Free Models**. It is designed for installation, configuration, teaching, demo workflows, and cost-safe AI-agent experimentation.

### Core Goals

1. Install and verify OpenClaw.
2. Connect OpenRouter safely.
3. Use free or cost-sensitive models with lower billing risk.
4. Build workflows for Dashboard, Telegram, Web Search, Cron, and local files.
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

## Repository Files

| File | Purpose |
|---|---|
| `README.md` | Main repository landing page and quick operating guide. |
| `assets/openclaw-openrouter-free-model-banner.svg` | Repository title / brand banner for the README header. |
| `openclaw_openrouter_free_model_installation_manual_th.md` | English (US) installation and operation manual. The filename is preserved for backward compatibility. |
| `openclaw_openai_gpt_5_x_lesson_th.md` | English (US) instructor lesson plan. The filename is preserved for backward compatibility. |
| `LICENSE` | MIT License. |

> Note: Some filenames still contain `_th` because earlier versions were written in Thai. The content has been converted to English (US). Rename files only after confirming that no external links depend on the current filenames.

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

---

## OpenRouter Setup

1. Sign in to OpenRouter.
2. Create an API key.
3. Store the key in a password manager.
4. Connect the provider in OpenClaw.

```bash
openclaw models auth login --provider openrouter
openclaw models auth list
openclaw models status
openclaw models status --probe
```

Alternative API-key onboarding flow:

```bash
export OPENROUTER_API_KEY="<your-openrouter-api-key>"
openclaw onboard --auth-choice apiKey --token-provider openrouter --token "$OPENROUTER_API_KEY"
```

---

## Model Strategy

OpenClaw uses provider-qualified model references:

```text
provider/model
```

For OpenRouter models, use a fully qualified reference:

```text
openrouter/<provider>/<model-id>
openrouter/<provider>/<model-id>:free
```

Recommended workflow:

```bash
openclaw models list --provider openrouter
openclaw models scan
openclaw models set "openrouter/<provider>/<model-id>:free"
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

Recommended practice:

- Store keys in a password manager.
- Use environment variables only when needed.
- Do not paste secrets into public chat or GitHub files.
- Rotate a key immediately if it may have been exposed.
- Review logs before sharing them.
- Back up configuration files before making changes.

---

## Troubleshooting

| Symptom | Likely Cause | Recommended Action |
|---|---|---|
| `401` or missing authentication | API key not configured or expired | Re-check provider auth and rotate the key if needed. |
| Gateway not running | OpenClaw service stopped | Run `openclaw gateway status` and restart the gateway. |
| `web_search is disabled` | No web provider configured | Run `openclaw configure --section web`. |
| Billing or credit warning | Paid model selected accidentally | Verify the selected model and fallback list. |
| Slow response | Free model rate limits or provider congestion | Switch to another verified free model or reduce workload. |
| Context overflow | Prompt, history, or files are too large | Summarize first, split the task, or reduce output length. |

---

## Workshop Mode

Recommended 90–180 minute workshop flow:

1. Explain AI-agent architecture.
2. Install and verify OpenClaw.
3. Connect OpenRouter.
4. Select a free/cost-safe model.
5. Run a short Dashboard or CLI demo.
6. Discuss security and token hygiene.
7. Assign a small lab using safe dummy data.

Suggested lab:

```text
Create a short AI-agent workflow that summarizes a local Markdown note,
classifies it into a category, and writes a short output file.
Do not use real secrets, customer data, or private files.
```

---

## External References

Use the official OpenClaw and OpenRouter documentation as the primary source for current commands, provider behavior, and model availability.

Recommended source categories:

- OpenClaw installation and CLI documentation
- OpenClaw model provider documentation
- OpenRouter provider documentation
- OpenRouter model catalog and free-model router documentation
- OpenRouter API quickstart documentation

---

## License

This repository is released under the MIT License. See [`LICENSE`](LICENSE) for details.
