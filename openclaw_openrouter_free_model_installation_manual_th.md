# OpenClaw + OpenRouter Free Model Installation Manual

> Document version: v1.3  
> Updated: August 1, 2026  
> Author: AorAke  
> Language: English (US), with Thai inline teaching comments in code blocks  
> Use case: installation guide, teaching handout, workshop runbook, and operational reference  
> Target audience: IT instructors, IT / CS / Software Engineering / Digital Business students, and beginners building AI-agent workflows  
> Scope: macOS first; adaptable to Linux and WSL2  
> Source basis: OpenClaw documentation, OpenRouter documentation, OpenRouter Free Models Router, and OpenRouter Quickstart

---

## Table of Contents

1. [Purpose of This Manual](#purpose-of-this-manual)
2. [What Changed in v1.3](#what-changed-in-v13)
3. [Chapter 1: OpenClaw + OpenRouter Overview](#chapter-1-openclaw--openrouter-overview)
4. [Chapter 2: Key Terms](#chapter-2-key-terms)
5. [Chapter 3: Understanding OpenRouter Free Models](#chapter-3-understanding-openrouter-free-models)
6. [Chapter 4: Preparing the Computer](#chapter-4-preparing-the-computer)
7. [Chapter 5: Installing OpenClaw](#chapter-5-installing-openclaw)
8. [Chapter 6: OpenRouter Onboarding](#chapter-6-openrouter-onboarding)
9. [Chapter 7: Configuring Free Models in OpenClaw](#chapter-7-configuring-free-models-in-openclaw)
10. [Chapter 8: Checking Model and Authentication Status](#chapter-8-checking-model-and-authentication-status)
11. [Chapter 9: Configuring Web Search](#chapter-9-configuring-web-search)
12. [Chapter 10: Creating a Cost-Safe Cron Job](#chapter-10-creating-a-cost-safe-cron-job)
13. [Chapter 11: Working with Files and Workspaces](#chapter-11-working-with-files-and-workspaces)
14. [Chapter 12: Telegram Recovery](#chapter-12-telegram-recovery)
15. [Chapter 13: Cost Control and Rate Limits](#chapter-13-cost-control-and-rate-limits)
16. [Chapter 14: Context Overflow Playbook](#chapter-14-context-overflow-playbook)
17. [Chapter 15: Security and Token Hygiene](#chapter-15-security-and-token-hygiene)
18. [Chapter 16: Troubleshooting](#chapter-16-troubleshooting)
19. [Chapter 17: Instructor Workshop Plan](#chapter-17-instructor-workshop-plan)
20. [Chapter 18: Student Assignment and Lab](#chapter-18-student-assignment-and-lab)
21. [Chapter 19: Command Cheat Sheet](#chapter-19-command-cheat-sheet)
22. [External References](#external-references)
23. [Summary](#summary)

---

## Purpose of This Manual

This manual helps learners install and operate **OpenClaw** with **OpenRouter Free Models** in a controlled, security-first, and cost-aware way.

The manual focuses on four practical goals:

1. Install and verify OpenClaw.
2. Connect OpenRouter using OAuth or an API key.
3. Configure a free-only or cost-safe model strategy.
4. Use the setup for workshops, labs, and lightweight automation.

After completing this manual, learners should be able to:

- Explain the OpenClaw + OpenRouter architecture.
- Distinguish an OpenRouter model slug from an OpenClaw model ref.
- Install OpenClaw and open the Dashboard/Gateway.
- Connect OpenRouter through OAuth or an API key.
- Configure a Free Model Router or a free model variant.
- Check model and authentication status with `openclaw models status`, `models list`, `models scan`, and `models status --probe`.
- Create a token-conscious Cron job.
- Apply basic security, rate-limit, and context-overflow controls.

---

## What Changed in v1.3

Version v1.3 improves all code blocks for GitHub readability.

| Improvement | Reason |
|---|---|
| Explicit language tags on code fences | Enables GitHub syntax highlighting and reduces unreadable plain-text blocks. |
| Thai `# XXX:` comments inside code blocks | Helps Thai instructors explain commands line by line during class. |
| Clearer separation between commands and expected output | Prevents learners from copying output text as commands. |
| Security-focused comments | Reinforces that API keys and tokens must never be exposed. |

---

# Chapter 1: OpenClaw + OpenRouter Overview

## 1.1 What Is OpenClaw?

OpenClaw is an AI-agent gateway that runs on the user's machine or server. It can connect a user interface, an agent session, a model provider, and tools such as Web Search, local files, Cron, and messaging channels.

```text
# XXX: แผนภาพนี้ใช้สอนลำดับการไหลของคำสั่งจากผู้ใช้ไปยัง gateway, model และ tool layer
User / Telegram / Dashboard
        ↓
OpenClaw Gateway
        ↓
Agent Session
        ↓
Model Provider such as OpenRouter
        ↓
Tools / Files / Web / Cron / Logs
```

## 1.2 What Is OpenRouter?

OpenRouter is a model-routing service that exposes many AI models behind a single API. Instead of creating separate keys for each model vendor, a user can access many model families through OpenRouter.

## 1.3 Why Use Free Models?

Free models are useful for:

- Teaching demonstrations
- Basic prompt testing
- Lightweight classification
- Low-risk daily briefs
- Introductory automation labs

Free models are not ideal for:

- Mission-critical production workloads
- Highly sensitive data
- Long-running high-volume tasks
- Workloads that require guaranteed latency or availability

---

# Chapter 2: Key Terms

| Term | Meaning |
|---|---|
| **Gateway** | The OpenClaw service that routes user requests to agents, models, and tools. |
| **Dashboard** | A local web interface for viewing or controlling OpenClaw. |
| **Provider** | A model service such as OpenRouter, OpenAI, Anthropic, Gemini, or a local endpoint. |
| **Model slug** | A model identifier used by a provider, such as `qwen/qwen3.6-plus:free`. |
| **Model ref** | A provider-qualified OpenClaw reference, such as `openrouter/qwen/qwen3.6-plus:free`. |
| **Free Model Router** | A router that selects from free models based on availability and request requirements. |
| **Cron job** | A scheduled automation task. |
| **Context overflow** | A failure caused by prompt, history, tool input, or output budget exceeding the model context limit. |

### Instructor Note

Ask learners to explain why `qwen/qwen3.6-plus:free` and `openrouter/qwen/qwen3.6-plus:free` are not the same operational reference inside OpenClaw.

---

# Chapter 3: Understanding OpenRouter Free Models

OpenRouter can expose free models in two common ways:

| Type | Example | Meaning | Best For |
|---|---|---|---|
| Free router | `openrouter/free` | OpenRouter chooses a free model that can handle the request. | Quick tests and teaching demos. |
| Free model variant | `<provider>/<model-id>:free` | A specific model variant marked as free. | More controlled demos and repeatable labs. |

## Important Limitations

Free models may have:

- Lower rate limits
- Higher latency
- Temporary unavailability
- Changing context windows
- Changing tool support
- Provider-side changes without notice

## Free-Only Caution

If strict free-only behavior is required, avoid ambiguous routing choices. Always verify the current catalog before a workshop.

---

# Chapter 4: Preparing the Computer

## 4.1 Check macOS and Node.js

Open Terminal and run:

```bash
# XXX: ตรวจสอบเวอร์ชัน macOS เพื่อช่วยวิเคราะห์ปัญหา compatibility ระหว่างติดตั้ง
sw_vers

# XXX: ตรวจสอบ Node.js และ npm เพราะ OpenClaw ใช้ runtime/installer ที่เกี่ยวข้องกับ Node ecosystem
node --version
npm --version
```

Recommended baseline:

```text
# XXX: ใช้ Node.js LTS หรือเวอร์ชันที่ OpenClaw installer แนะนำ เพื่อให้ command line tools ทำงานเสถียร
Use an active Node.js LTS version or the version recommended by the OpenClaw installer.
```

If Node.js is missing on macOS:

```bash
# XXX: ติดตั้ง Node.js ผ่าน Homebrew เฉพาะกรณีที่เครื่องยังไม่มี Node.js
brew install node
```

Check again:

```bash
# XXX: ตรวจซ้ำหลังติดตั้ง เพื่อยืนยันว่า shell เห็น node และ npm แล้ว
node --version
npm --version
which node
which npm
```

## 4.2 Classroom Preparation Checklist

Before class, the instructor should verify:

- Internet access
- Node.js availability
- Browser access to the local Dashboard
- OpenRouter account access
- Test API key or safe demo key
- No real secrets shown on slides
- Backup command examples ready

---

# Chapter 5: Installing OpenClaw

## 5.1 Recommended Method: Installer Script

```bash
# XXX: ใช้วิธีนี้เมื่ออยากติดตั้งแบบรวดเร็วตาม installation script ของ OpenClaw
curl -fsSL https://openclaw.ai/install.sh | bash
```

The installer may check system requirements, install required components, install OpenClaw, and begin onboarding.

## 5.2 Alternative Method: npm

Use this if Node.js and npm are already available:

```bash
# XXX: ติดตั้ง package OpenClaw แบบ global เพื่อให้เรียกคำสั่ง openclaw ได้จากทุก path
npm install -g openclaw@latest

# XXX: เริ่ม onboarding และติดตั้ง daemon/service สำหรับใช้งาน agent gateway ต่อเนื่อง
openclaw onboard --install-daemon
```

## 5.3 Verify the Installation

```bash
# XXX: ตรวจสอบเวอร์ชันและ health check หลังติดตั้งเสร็จ
openclaw --version
openclaw doctor
openclaw gateway status
```

Expected result:

```text
# XXX: ผลลัพธ์นี้เป็นตัวอย่าง ไม่ต้อง copy ไปรันใน terminal
Gateway: running
Dashboard: http://127.0.0.1:18789/
Connectivity probe: ok
```

## 5.4 Open the Dashboard

```bash
# XXX: เปิด Dashboard ผ่านคำสั่ง OpenClaw เพื่อใช้ตรวจสถานะและสาธิต UI
openclaw dashboard
```

Or open the local URL:

```bash
# XXX: เปิด URL local โดยตรงบน macOS กรณีคำสั่ง dashboard ไม่เปิด browser อัตโนมัติ
open http://127.0.0.1:18789
```

---

# Chapter 6: OpenRouter Onboarding

## 6.1 Create an OpenRouter API Key

1. Sign in to OpenRouter.
2. Open the **API Keys** page.
3. Create a new key.
4. Give the key a clear name, such as `OpenClaw-Workshop-Free-Model`.
5. Copy the key and store it in a password manager.

Common key pattern:

```text
# XXX: ตัวอย่างรูปแบบ key เท่านั้น ห้ามใส่ API key จริงในเอกสารหรือ repository
sk-or-v1-...
```

Do not confuse this with keys from other providers, such as:

```text
# XXX: ตัวอย่าง pattern ของ provider อื่น เพื่อช่วยแยกประเภท key ระหว่างสอน
hf_...       # Hugging Face-style key
sk-proj-...  # OpenAI-style project key
```

## 6.2 Authenticate OpenRouter in OpenClaw

```bash
# XXX: login provider OpenRouter ผ่าน OpenClaw เพื่อบันทึกสิทธิ์การเชื่อมต่อแบบปลอดภัย
openclaw models auth login --provider openrouter
```

Alternative onboarding method:

```bash
# XXX: กำหนด API key เป็น environment variable ชั่วคราว ห้าม commit ค่า key จริงลง GitHub
export OPENROUTER_API_KEY="<your-openrouter-api-key>"

# XXX: ระบุ token-provider เป็น openrouter เพื่อให้ OpenClaw รู้ว่า key นี้ใช้กับ provider ใด
openclaw onboard --auth-choice apiKey --token-provider openrouter --token "$OPENROUTER_API_KEY"
```

## 6.3 Verify Authentication

```bash
# XXX: ตรวจว่ามี auth profile ของ provider แล้วหรือไม่
openclaw models auth list

# XXX: ตรวจสถานะ model routing และ probe การเรียกใช้งานจริง
openclaw models status
openclaw models status --probe
```

### Instructor Warning

Never type a real API key into a projected screen, shared terminal, public repository, or class chat.

---

# Chapter 7: Configuring Free Models in OpenClaw

## 7.1 List Available OpenRouter Models

```bash
# XXX: แสดงรายการโมเดล OpenRouter ที่ระบบมองเห็นก่อนเลือกใช้ใน class/demo
openclaw models list --provider openrouter
```

## 7.2 Scan Model Availability

```bash
# XXX: scan catalog/availability เพื่อหลีกเลี่ยงการใช้ model ref ที่หมดอายุหรือไม่พร้อมใช้งาน
openclaw models scan
```

## 7.3 Choose a Free Model

Use the current model catalog instead of hardcoding a model permanently.

Example pattern:

```bash
# XXX: ตั้ง primary model ด้วย full model ref และใช้ :free เมื่อ workshop ต้องการควบคุมต้นทุน
openclaw models set "openrouter/<provider>/<model-id>:free"
```

## 7.4 Set a Fallback

```bash
# XXX: ล้าง fallback เดิมก่อนเพื่อไม่ให้เผลอเรียก paid model ที่เคยตั้งไว้
openclaw models fallbacks clear

# XXX: เพิ่ม fallback ที่ยืนยันแล้วว่าเป็น free/cost-safe model
openclaw models fallbacks add "openrouter/<provider>/<fallback-model-id>:free"
```

## 7.5 Add an Alias

```bash
# XXX: สร้าง alias เพื่อเรียก model ref ยาว ๆ ได้ง่ายในการสอนหรือ demo
openclaw models aliases add or-free "openrouter/<provider>/<model-id>:free"

# XXX: ตรวจ alias ทั้งหมดเพื่อป้องกันสับสนระหว่างชื่อสั้นกับ model ref จริง
openclaw models aliases list
```

## 7.6 Restart and Probe

```bash
# XXX: restart gateway หลังเปลี่ยน model configuration เพื่อโหลดค่าล่าสุด
openclaw gateway restart

# XXX: probe อีกครั้งเพื่อยืนยันว่า configuration ใช้งานได้จริง
openclaw models status --probe
```

### Safe Classroom Model Policy

```text
# XXX: นโยบายนี้ใช้ควบคุมความเสี่ยงด้านค่าใช้จ่ายและ token ระหว่างสอน
Use a verified free model.
Use a verified free fallback.
Keep output short.
Minimize tool calls.
Avoid full-PDF reading.
Do not run Cron too frequently.
```

---

# Chapter 8: Checking Model and Authentication Status

Use these commands after any provider or model change:

```bash
# XXX: ตรวจ authentication, catalog และสถานะการเรียกใช้งานหลังแก้ config ทุกครั้ง
openclaw models auth list
openclaw models list --provider openrouter
openclaw models status
openclaw models status --probe
```

A good result should show:

```text
# XXX: ตัวอย่างผลลัพธ์ที่บอกว่า provider พร้อมใช้งานแล้ว
Provider: openrouter
Auth: configured
Primary model: configured
Probe: ok
```

If the probe fails, check:

1. The API key.
2. The provider name.
3. The model ref.
4. Gateway restart status.
5. Rate-limit or billing messages.

---

# Chapter 9: Configuring Web Search

If Web Search is disabled:

```text
# XXX: ข้อความนี้เป็น error sample แปลว่ายังไม่มี web provider สำหรับ tool web_search
web_search is disabled or no provider is available
```

Configure the web section:

```bash
# XXX: เปิดส่วนตั้งค่า web provider เพื่อให้ agent ใช้ web_search ได้
openclaw configure --section web

# XXX: restart gateway เพื่อโหลด web provider configuration ใหม่
openclaw gateway restart
```

| Provider | Strength | Best For |
|---|---|---|
| DuckDuckGo | Quick testing, usually no API key | Classroom demo |
| Brave | API-based and more controlled | Practical workflows |
| Gemini Search | Grounding and citations | Research workflows |
| SearXNG | Self-hosted and privacy-oriented | Advanced users |

### Teaching Point

Students should distinguish:

- Answering from model memory
- Answering with web search
- Answering with grounded/cited search results

---

# Chapter 10: Creating a Cost-Safe Cron Job

Cron jobs are powerful but can consume tokens repeatedly. Design them conservatively.

## 10.1 Cost-Safe Design Rules

```text
# XXX: ใช้เป็น checklist ก่อนตั้ง cron เพื่อไม่ให้เกิดค่าใช้จ่ายหรือ token usage เกินจำเป็น
Limit the number of results.
Limit answer length.
Use isolated sessions.
Use a verified cost-safe model.
Do not read full large files.
Do not create long tool chains.
```

## 10.2 Example Daily Brief

```bash
# XXX: สร้างตัวแปร MSG ด้วย heredoc เพื่อเก็บ prompt ยาว ๆ อย่างเป็นระเบียบ
MSG=$(cat <<'EOF'
Create a lightweight daily brief.

Search only for important items from the last seven days.
Limit the result to three items.
Answer in English.
Keep the response under 700 words.
Do not read full PDFs.
Do not produce long analysis.
If no important item is found, say: "No major item met the criteria today."

Format:
1) Overall status
2) Key items
3) Short impact notes
4) Sources, if available
EOF
)

# XXX: เพิ่ม cron job แบบ cost-safe โดยจำกัดเวลา session channel model และข้อความที่ส่งให้ agent
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

## 10.3 Check Cron Jobs

```bash
# XXX: ดูรายการ cron job และทดสอบ run เฉพาะ job ที่เลือกอย่างควบคุมได้
openclaw cron list
openclaw cron run "<job-id>"
openclaw cron runs --id "<job-id>"
```

---

# Chapter 11: Working with Files and Workspaces

## 11.1 Safe File Workflow

```text
# XXX: หลักการทำงานกับไฟล์ต้องอ่านก่อน ยืนยัน path ก่อน และ backup ก่อนแก้ไขเสมอ
Read first → confirm path → back up before editing → write narrowly → verify result
```

## 11.2 Create a Workspace

```bash
# XXX: สร้าง folder input/output แยกกัน เพื่อไม่ให้ไฟล์ต้นฉบับปนกับผลลัพธ์จาก agent
mkdir -p "$HOME/AI-Agent-Lab/input"
mkdir -p "$HOME/AI-Agent-Lab/output"
```

## 11.3 Read Files

```bash
# XXX: อ่านไฟล์ตัวอย่างทั้งไฟล์หรือบางส่วน เพื่อประเมินขนาดและเนื้อหาก่อนให้ agent ทำงาน
cat "$HOME/AI-Agent-Lab/input/sample.txt"
head -80 "$HOME/AI-Agent-Lab/input/sample.txt"
tail -80 "$HOME/AI-Agent-Lab/input/sample.txt"
```

## 11.4 Search Files

```bash
# XXX: ค้นหารายชื่อไฟล์ใน workspace โดยจำกัด depth เพื่อลด noise
find "$HOME/AI-Agent-Lab" -maxdepth 3 -type f -print

# XXX: กรองเฉพาะ Markdown file เมื่อจะทำงานกับเอกสาร .md
find "$HOME/AI-Agent-Lab" -maxdepth 3 -type f -name "*.md" -print

# XXX: ค้น keyword แบบ recursive พร้อม line number เพื่อใช้ชี้ตำแหน่งหลักฐานในไฟล์
 grep -Rni "keyword" "$HOME/AI-Agent-Lab"
```

## 11.5 Write a Markdown File

```bash
# XXX: เขียนไฟล์ Markdown ตัวอย่างด้วย heredoc โดยไม่แตะไฟล์ต้นฉบับใน input
cat <<'EOF' > "$HOME/AI-Agent-Lab/output/summary.md"
# Summary

This is a sample summary.
EOF
```

## 11.6 Back Up Before Editing

```bash
# XXX: backup ไฟล์พร้อม timestamp ก่อนแก้ไข เพื่อย้อนกลับได้หากผลลัพธ์ผิดพลาด
cp "$HOME/AI-Agent-Lab/output/summary.md" \
   "$HOME/AI-Agent-Lab/output/summary.backup.$(date +%Y%m%d-%H%M%S).md"
```

---

# Chapter 12: Telegram Recovery

If the Telegram session is stuck, start a new session:

```text
# XXX: คำสั่งนี้ใช้ reset session ใน Telegram เมื่อบทสนทนาหรือ context เริ่มค้าง
/new
```

Then send a short test message:

```text
# XXX: ข้อความทดสอบสั้น ๆ เพื่อดูว่า agent กลับมาตอบสนองตามปกติหรือไม่
Check the system status briefly.
```

If the problem continues:

```bash
# XXX: ตรวจ gateway, probe โมเดล และดู log เพื่อหา root cause ของปัญหา
openclaw gateway status
openclaw models status --probe
openclaw logs --follow
```

---

# Chapter 13: Cost Control and Rate Limits

## 13.1 Cost Control Checklist

- Use a verified free model.
- Use a free fallback model.
- Limit answer length.
- Avoid large full-document reads.
- Avoid broad web searches.
- Use isolated sessions for scheduled jobs.
- Avoid high-frequency Cron jobs.

## 13.2 When a Rate Limit Appears

If you see a rate-limit message:

1. Wait before retrying.
2. Reduce prompt size.
3. Reduce output length.
4. Switch to a fallback model.
5. Avoid repeated manual retries.

Example wait command:

```bash
# XXX: หน่วงเวลาก่อน probe ซ้ำ เพื่อลดการยิง request ถี่เกินไปเมื่อเจอ rate limit
sleep 90
openclaw models status --probe
```

---

# Chapter 14: Context Overflow Playbook

Context overflow happens when the total input and expected output exceed a model's context window.

Common causes:

- Long chat history
- Full PDF or large file input
- Multiple web pages
- Excessive tool output
- Too much requested output

Recommended fixes:

```text
# XXX: วิธีลด context overflow คือแยกงานเป็นชิ้นเล็ก ลด input และจำกัด output
Start a new session.
Summarize first, then analyze.
Split the task into smaller parts.
Read only the necessary file section.
Limit output length.
Avoid unnecessary tool calls.
```

Telegram recovery:

```text
# XXX: reset session เพื่อเริ่ม context ใหม่เมื่อ context เดิมใหญ่เกินไปหรือค้าง
/new
```

---

# Chapter 15: Security and Token Hygiene

Never expose:

```text
# XXX: รายการเหล่านี้เป็น secret หรือข้อมูลอ่อนไหว ห้ามนำขึ้น GitHub หรือแชร์หน้าจอจริง
API keys
Gateway tokens
Telegram bot tokens
Passwords
Session tokens
.env files
auth profiles
openclaw.json files containing secrets
```

## 15.1 Safe Practices

- Use a password manager.
- Do not share secrets in chat.
- Do not commit secrets to GitHub.
- Rotate any potentially exposed key immediately.
- Back up configuration before editing.
- Review logs before sharing them.

## 15.2 Back Up Configuration

```bash
# XXX: สำรองไฟล์ config ก่อนแก้ไข เพราะ openclaw.json อาจมีค่า provider/model/tool ที่สำคัญ
cp "$HOME/.openclaw/openclaw.json" \
   "$HOME/.openclaw/openclaw.backup.$(date +%Y%m%d-%H%M%S).json"
```

## 15.3 Sanitize Logs Before Sharing

Before sending logs to another person, remove:

- API keys
- Tokens
- User IDs
- Chat IDs
- Local file paths containing sensitive names
- Customer or student data

---

# Chapter 16: Troubleshooting

| Symptom | Likely Cause | Recommended Fix |
|---|---|---|
| `401` or missing authentication | API key is missing or not loaded | Re-authenticate the provider and restart the gateway. |
| Billing or credit error | Paid model selected or insufficient credit | Switch to a verified free model and reduce token use. |
| `Unknown model` | Invalid or stale model ref | Run `openclaw models list --provider openrouter` and update the model ref. |
| `web_search disabled` | Web provider not configured | Run `openclaw configure --section web`. |
| Context overflow | Prompt, files, and history are too large | Use `/new`, reduce context, and split the task. |
| Cron repeats too often | Schedule is too aggressive | Lower frequency and reduce output length. |
| Telegram does not respond | Stuck session or gateway issue | Send `/new`, then check gateway and logs. |

---

# Chapter 17: Instructor Workshop Plan

Suggested three-hour workshop:

| Time | Activity | Instructor Goal |
|---|---|---|
| 0:00–0:15 | Course opening | Explain what learners will build. |
| 0:15–0:35 | Architecture overview | Distinguish gateway, provider, model, and tool. |
| 0:35–1:00 | Installation | Install and verify OpenClaw. |
| 1:00–1:25 | OpenRouter onboarding | Authenticate OpenRouter safely. |
| 1:25–1:50 | Model configuration | Set and probe a free model. |
| 1:50–2:15 | Dashboard or Telegram demo | Show a real agent interaction. |
| 2:15–2:40 | Cron automation | Create a lightweight scheduled task. |
| 2:40–2:55 | Troubleshooting | Review common errors. |
| 2:55–3:00 | Wrap-up | Reinforce security and cost control. |

---

# Chapter 18: Student Assignment and Lab

## Lab 1: Architecture Diagram

Draw the OpenClaw + OpenRouter workflow and label:

- User interface
- Gateway
- Agent session
- Model provider
- Tool layer
- Output

## Lab 2: Safe Model Strategy

Write a model strategy for a classroom demo:

```text
# XXX: กรอก strategy แบบสั้นเพื่อฝึกคิดเรื่อง primary/fallback model และ safety controls
Primary model:
Fallback model:
Output limit:
Tool-call limit:
Safety notes:
```

## Lab 3: Cost-Safe Prompt

Create a prompt that asks the agent to produce a daily brief under 500 words without reading full PDFs.

## Lab 4: Troubleshooting Scenario

Given this error:

```text
# XXX: ตัวอย่าง error สำหรับฝึกวิเคราะห์ model ref หรือ catalog ที่ไม่ถูกต้อง
Unknown model
```

Students must propose at least three checks and one corrective command.

---

# Chapter 19: Command Cheat Sheet

```bash
# XXX: System commands ใช้ตรวจสถานะพื้นฐานของ OpenClaw และ gateway
openclaw --version
openclaw doctor
openclaw gateway status
openclaw gateway restart
openclaw dashboard

# XXX: Model commands ใช้ login, list, scan, probe, set model, fallback และ alias
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

# XXX: Web Search commands ใช้ตั้งค่า web provider และ reload gateway หลังแก้ config
openclaw configure --section web
openclaw gateway restart

# XXX: Cron commands ใช้ตรวจ, run, ดูประวัติ, disable และ enable scheduled jobs
openclaw cron list
openclaw cron run "<job-id>"
openclaw cron runs --id "<job-id>"
openclaw cron disable "<job-id>"
openclaw cron enable "<job-id>"

# XXX: Log commands ใช้ดูวิธีเรียก log และ follow log เพื่อ debug ปัญหา runtime
openclaw logs --help
openclaw logs --follow
```

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

## Summary

This manual provides a classroom-ready and operations-ready path for using OpenClaw with OpenRouter Free Models. It emphasizes practical setup, verified model references, security, cost control, and repeatable workshops.
