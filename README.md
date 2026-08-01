# OpenClaw + OpenRouter Free Model

![Documentation](https://img.shields.io/badge/type-documentation-blue)
![OpenClaw](https://img.shields.io/badge/platform-OpenClaw-purple)
![OpenRouter](https://img.shields.io/badge/provider-OpenRouter-orange)
![Language](https://img.shields.io/badge/language-Thai-lightgrey)
![Security](https://img.shields.io/badge/security-first-critical)

> คู่มือมาตรฐานภาษาไทยสำหรับติดตั้ง กำหนดค่า ใช้งาน สอน และดูแล **OpenClaw** ร่วมกับ **OpenRouter Free Model** โดยเน้นการใช้งานจริง การควบคุมต้นทุน ความปลอดภัยของ API Key และการนำไปใช้เป็น workshop สำหรับผู้เรียนด้าน IT / AI Agent / Automation

---

## Overview

**OpenClaw** คือระบบ AI Agent ที่ทำหน้าที่เป็น gateway ระหว่างผู้ใช้ ช่องทางสื่อสาร เครื่องมือ และ model provider หลายรูปแบบ เช่น OpenRouter, OpenAI, Anthropic, Gemini หรือ local model ผ่าน endpoint ที่กำหนดได้

Repository นี้จัดทำขึ้นเพื่อเป็นคู่มือใช้งาน OpenClaw กับ **OpenRouter Free Model** โดยมีเป้าหมายหลัก 4 ด้าน:

1. ติดตั้งและตรวจสอบ OpenClaw ให้พร้อมใช้งาน
2. เชื่อมต่อ OpenRouter อย่างปลอดภัย
3. เลือกใช้ free model หรือ cost-sensitive model โดยลดความเสี่ยงด้านค่าใช้จ่าย
4. สร้าง workflow สำหรับ Telegram, Dashboard, Web Search, Cron และไฟล์ local

---

## Who This Repository Is For

| กลุ่มผู้ใช้ | เหมาะกับ |
|---|---|
| ผู้เริ่มต้นใช้งาน AI Agent | ต้องการติดตั้ง OpenClaw และทดลอง OpenRouter |
| วิทยากร / อาจารย์ / Trainer | ต้องการคู่มือสอนแบบ step-by-step |
| นักศึกษา IT / CS / Software Engineering | ต้องการเข้าใจ architecture ของ AI Agent |
| Developer / Automation User | ต้องการตั้ง Cron, Telegram และ Web Search |
| HR / Compliance / Knowledge Worker | ต้องการใช้ AI Agent ช่วยสรุป ค้นหา และจัดหมวดข้อมูล |

---

## Key Concepts

| คำศัพท์ | ความหมาย |
|---|---|
| **OpenClaw Gateway** | ตัวกลางที่รับคำสั่งจากผู้ใช้และส่งงานไปยัง agent / model / tool |
| **Dashboard** | หน้าเว็บ local สำหรับตรวจสถานะและควบคุมระบบ |
| **Model Provider** | ผู้ให้บริการโมเดล เช่น OpenRouter, OpenAI, Anthropic, Gemini |
| **Model Ref** | รูปแบบอ้างอิงโมเดล เช่น `openrouter/<provider>/<model>` |
| **Agent Session** | session การสนทนาหรือการทำงานของ AI Agent |
| **Tool** | ความสามารถเสริม เช่น Web Search, File Search, Cron, local files |
| **Cron Automation** | งานอัตโนมัติตามเวลา เช่น daily brief หรือ monitoring |
| **Free Model** | โมเดลที่ OpenRouter แสดงว่าไม่มีค่าใช้จ่าย หรือมี suffix `:free` ตาม catalog ปัจจุบัน |

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

ให้อธิบายกับผู้เรียนว่า OpenClaw ไม่ใช่ตัวโมเดลโดยตรง แต่เป็น **agent gateway / orchestration layer** ที่เชื่อมผู้ใช้กับ model provider และ tools ต่าง ๆ

---

## Repository Scope

Repository นี้ครอบคลุม:

- OpenClaw installation baseline
- OpenRouter API key setup
- Model configuration concept
- Free model usage pattern
- Web Search configuration
- Cron automation pattern
- Telegram recovery
- File workflow
- Security and token hygiene
- Troubleshooting
- Workshop checklist
- Command cheat sheet

Repository นี้ **ไม่ควร** ใช้เก็บข้อมูลต่อไปนี้:

- API Key จริง
- Telegram Bot Token จริง
- Gateway Token จริง
- Password
- `.env` ที่มี secret
- ไฟล์ลูกค้า / ข้อมูลส่วนบุคคล / confidential data

---

## Requirements

### Recommended Environment

| Component | Recommendation |
|---|---|
| OS | macOS เป็นหลัก; Linux / WSL2 ปรับใช้ได้ |
| Node.js | ใช้ LTS หรือ version ที่ OpenClaw รองรับในเวลาติดตั้ง |
| npm | ใช้ version ที่มาพร้อม Node.js |
| Browser | Chrome / Edge / Safari สำหรับ Dashboard |
| OpenRouter Account | ต้องมีบัญชีเพื่อสร้าง API Key |
| Password Manager | แนะนำให้ใช้เก็บ API Key |

### Pre-check

```bash
sw_vers
node --version
npm --version
which node
which npm
```

หากยังไม่มี Node.js บน macOS:

```bash
brew install node
```

---

## Quick Start

> เหมาะสำหรับ demo หรือ workshop แบบเร็ว

```bash
# 1) Install OpenClaw
npm install -g openclaw@latest

# 2) Start onboarding
openclaw onboard --install-daemon

# 3) Check system status
openclaw --version
openclaw doctor
openclaw gateway status

# 4) Open Dashboard
openclaw dashboard
```

หลังจากนั้นให้เชื่อม OpenRouter API Key และตรวจ model status:

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

> หากผลลัพธ์ไม่ตรง ให้ไปที่ [Troubleshooting](#troubleshooting)

---

## Dashboard and Gateway

### Open Dashboard

```bash
openclaw dashboard
```

หรือเปิดผ่าน browser:

```bash
open http://127.0.0.1:18789
```

### Restart Gateway

```bash
openclaw gateway restart
```

> หลีกเลี่ยงคำสั่งที่ไม่ถูกต้อง เช่น `openclaw restart` ให้ใช้ `openclaw gateway restart`

### Health Check

```bash
openclaw gateway status
openclaw models status
openclaw models status --probe
openclaw cron list
```

---

## OpenRouter Setup

### 1. Create OpenRouter API Key

1. Login OpenRouter
2. ไปที่เมนู **API Keys**
3. Create new key
4. ตั้งชื่อ เช่น `OpenClaw-Teaching-Free-Model`
5. Copy key และเก็บใน password manager

รูปแบบ key โดยทั่วไป:

```text
sk-or-v1-...
```

### 2. Connect OpenClaw with OpenRouter

วิธีผ่าน OpenClaw model auth:

```bash
openclaw models auth login --provider openrouter
```

อีกแนวทางที่ OpenRouter document ระบุสำหรับ onboarding:

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
Verify free model status before classroom demo.
Keep fallback model cost-safe.
```

### Model Ref Pattern

OpenClaw ใช้แนวคิด model ref แบบ `provider/model`

ตัวอย่าง:

```text
openrouter/<provider>/<model-id>
openrouter/<provider>/<model-id>:free
```

หาก model ID ของ OpenRouter มี `/` อยู่ภายใน ให้ใส่ provider prefix ให้ครบ เช่น:

```text
openrouter/moonshotai/kimi-k2
```

### Find Available Models

```bash
openclaw models list --provider openrouter
openclaw models scan
```

### Set Primary Model

ให้เลือก model จากรายการจริงที่ตรวจพบก่อน แล้วแทนค่าลงในคำสั่งนี้:

```bash
openclaw models set "openrouter/<provider>/<model-id>:free"
```

### Set Fallback

```bash
openclaw models fallbacks clear
openclaw models fallbacks add "openrouter/<provider>/<fallback-model-id>:free"
```

### Add Alias

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

สำหรับห้องเรียนหรือ workshop ให้ใช้แนวทางนี้:

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

Free model อาจเปลี่ยน availability, rate limit, latency, context size หรือ tool support ได้ จึงควรตรวจสอบก่อนสอนทุกครั้ง

### Rules

1. ตรวจ catalog ก่อน demo
2. อย่าใช้ `auto` หากต้องการควบคุม free-only อย่างเข้มงวด
3. ใช้ exact model ref เมื่อสอนนิสิต
4. จำกัด prompt และ output
5. หลีกเลี่ยงการสั่ง agent อ่านไฟล์ใหญ่ทั้งฉบับ
6. ไม่รัน Cron ถี่เกินจำเป็น
7. ตรวจ logs หลัง demo

### Demo Prompt

```text
ตอบเป็นภาษาไทยแบบสั้น ไม่เกิน 5 bullet
อธิบายว่า OpenClaw กับ OpenRouter ทำงานร่วมกันอย่างไร
ห้ามเรียก tool ภายนอก
```

---

## Web Search

ถ้าเจอ error:

```text
web_search is disabled or no provider is available
```

ให้ตั้งค่า web provider:

```bash
openclaw configure --section web
openclaw gateway restart
```

### Provider Selection

| Provider | จุดเด่น | เหมาะกับ |
|---|---|---|
| DuckDuckGo | ทดสอบเร็ว ไม่ต้องใช้ API Key | Classroom demo |
| Brave | API-based และคุมคุณภาพได้ดีขึ้น | Production-like workflow |
| Gemini Search | เหมาะกับ grounding / citation | Research workflow |
| SearXNG | self-host / privacy | Advanced user |

### Teaching Note

ให้นักศึกษาแยกให้ออกระหว่าง:

- Model answering from memory
- Model answering with web search
- Model answering with cited / grounded information

---

## Cron Automation

Cron เหมาะกับงานซ้ำ เช่น daily brief, file reminder, monitoring หรือ summary

### Cost-Safe Cron Design

```text
จำกัดจำนวนผลลัพธ์
จำกัดความยาวคำตอบ
ใช้ session isolated
ใช้ model cost-safe
ไม่อ่านไฟล์ยาวเต็มฉบับ
ไม่ทำ chain of tools ยาวเกินไป
```

### Example: Daily Ultra-light Brief

```bash
MSG=$(cat <<'EOF'
ทำ Daily Brief แบบ Ultra-light

เงื่อนไข:
- สรุปเฉพาะประเด็นสำคัญไม่เกิน 3 รายการ
- ตอบภาษาไทย
- ไม่เกิน 700 คำ
- ห้ามอ่าน PDF เต็ม
- ห้ามทำบทวิเคราะห์ยาว
- ถ้าไม่พบข้อมูล ให้ตอบว่า “ไม่พบรายการสำคัญที่เข้าเกณฑ์”

รูปแบบผลลัพธ์:
1) สถานะรวม
2) รายการสำคัญ
3) ข้อควรติดตาม
EOF
)

openclaw cron add \
  --name "daily-ultra-light-brief" \
  --cron "0 8 * * *" \
  --tz "Asia/Bangkok" \
  --session isolated \
  --announce \
  --channel telegram \
  --to "<telegram-chat-id>" \
  --model "openrouter/<provider>/<model-id>:free" \
  --message "$MSG"
```

### Cron Management

```bash
openclaw cron list
openclaw cron run "<job-id>"
openclaw cron runs --id "<job-id>"
openclaw cron disable "<job-id>"
openclaw cron enable "<job-id>"
openclaw cron edit "<job-id>" --model "openrouter/<provider>/<model-id>:free"
```

---

## Telegram Recovery

เมื่อ Telegram session ค้างหรือ context เต็ม ให้ reset session:

```text
/new
```

แล้วทดสอบด้วยข้อความสั้น:

```text
ตรวจสถานะสั้น ๆ ว่าระบบพร้อมใช้งานหรือไม่
```

### Teaching Note

ให้อธิบายว่า `/new` เป็นการลดปัญหา context overflow และ stale session แต่ไม่ได้แก้ปัญหา API Key หรือ provider outage

---

## File Workflow

### Safety Principle

```text
Read before write.
Backup before edit.
Use explicit path.
Avoid secrets.
Review output.
```

### Create Workspace

```bash
mkdir -p "$HOME/AI-Agent-Lab/input"
mkdir -p "$HOME/AI-Agent-Lab/output"
mkdir -p "$HOME/AI-Agent-Lab/backup"
```

### Read Files

```bash
cat "$HOME/AI-Agent-Lab/input/sample.txt"
head -80 "$HOME/AI-Agent-Lab/input/sample.txt"
tail -80 "$HOME/AI-Agent-Lab/input/sample.txt"
```

### Search Files

```bash
find "$HOME/AI-Agent-Lab" -maxdepth 3 -type f -print
grep -Rni "keyword" "$HOME/AI-Agent-Lab/input"
```

### Write Markdown

```bash
cat <<'EOF' > "$HOME/AI-Agent-Lab/output/summary.md"
# Summary

This is a sample summary.
EOF
```

### Backup Before Edit

```bash
cp "$HOME/AI-Agent-Lab/output/summary.md" \
   "$HOME/AI-Agent-Lab/backup/summary.backup.$(date +%Y%m%d-%H%M%S).md"
```

---

## Prompt Pattern

ใช้ pattern นี้สำหรับสอนนักศึกษา:

```text
บทบาท:
คุณคือ...

งาน:
ต้องการให้ทำอะไร

ข้อมูล:
ข้อมูลที่ใช้ประกอบ

ข้อจำกัด:
ความยาว / ภาษา / ห้ามทำอะไร / ใช้แหล่งใด

รูปแบบผลลัพธ์:
หัวข้อ / ตาราง / JSON / bullet / checklist

เกณฑ์คุณภาพ:
ต้องตรวจอะไร / ต้องอ้างอิงอะไร / ต้องระบุข้อจำกัดอย่างไร
```

### Example

```text
บทบาท:
คุณคือผู้ช่วยสรุปข่าวเทคโนโลยีสำหรับนักศึกษา IT

งาน:
สรุปข่าว AI Agent ล่าสุดไม่เกิน 3 ข่าว

ข้อจำกัด:
- ตอบภาษาไทย
- ข่าวละไม่เกิน 4 บรรทัด
- ระบุแหล่งที่มาถ้ามี
- ถ้าข้อมูลไม่เพียงพอ ให้ระบุว่า “ข้อมูลไม่เพียงพอ”

รูปแบบผลลัพธ์:
1) ประเด็น
2) สรุป
3) ผลกระทบต่อผู้เรียน IT
```

---

## Security and Token Hygiene

### Never Commit These

```text
API Key
Gateway Token
Telegram Bot Token
Password
Session Token
.env
auth-profiles.json
openclaw.json ที่มี secret
client/customer files
personal data
```

### Checklist

```text
[ ] ไม่ paste API key ในแชตสาธารณะ
[ ] ไม่ commit .env
[ ] ใช้ password manager
[ ] backup config ก่อนแก้
[ ] ตรวจ logs ก่อนส่งต่อ
[ ] ปิด/ลบ key ที่สงสัยว่าหลุด
[ ] จำกัด scope ของ demo data
[ ] ใช้ synthetic data ใน classroom
```

### Backup Config

```bash
cp "$HOME/.openclaw/openclaw.json" \
   "$HOME/.openclaw/openclaw.backup.$(date +%Y%m%d-%H%M%S).json"
```

---

## Troubleshooting

| Symptom / Error | Possible Cause | Action |
|---|---|---|
| `401` | API Key ผิด / หมดอายุ | login provider ใหม่ / สร้าง key ใหม่ |
| `402` | credit ไม่พอ หรือ route ไป paid model | เปลี่ยนเป็น verified free model / ลด token |
| `rate limit` | ใช้งานถี่เกิน / free model จำกัด request | รอ / ลด tool call / เปลี่ยน model |
| `context overflow` | prompt + history + file ใหญ่เกิน | ใช้ `/new`, ลด prompt, แบ่งงาน |
| `web_search disabled` | ยังไม่ได้ตั้ง web provider | `openclaw configure --section web` |
| `unknown command restart` | ใช้คำสั่งผิด | ใช้ `openclaw gateway restart` |
| Dashboard เปิดไม่ได้ | gateway ไม่ทำงาน / port issue | `openclaw gateway status`, restart gateway |
| Cron ไม่ส่ง Telegram | channel / chat id / token ผิด | ตรวจ channel config และ cron runs |
| Model ไม่ตอบ | provider auth / model ref ผิด | `openclaw models status --probe` |

### Debug Commands

```bash
openclaw doctor
openclaw gateway status
openclaw models status
openclaw models status --probe
openclaw models auth list
openclaw cron list
openclaw logs --help
openclaw logs --follow
```

---

## Workshop Mode

### 90-Minute Workshop

| Time | Topic | Activity |
|---|---|---|
| 0–10 min | Concept | AI Agent, Gateway, Provider, Tool |
| 10–25 min | Install | CLI + Dashboard check |
| 25–40 min | OpenRouter | API Key + model status |
| 40–55 min | Prompt | short prompt + model test |
| 55–70 min | Web / File | demo tool boundary |
| 70–85 min | Cron | create safe daily brief |
| 85–90 min | Wrap-up | security checklist |

### 3-Hour Workshop

| Time | Topic | Activity |
|---|---|---|
| 0–20 min | Architecture | diagram + concept discussion |
| 20–45 min | Installation | install + gateway + dashboard |
| 45–75 min | Provider | OpenRouter setup + model refs |
| 75–105 min | Prompt Engineering | structured prompt lab |
| 105–135 min | Tools | web search / file workflow |
| 135–160 min | Cron | automation lab |
| 160–175 min | Security | token hygiene + threat scenarios |
| 175–180 min | Review | quiz + next steps |

---

## Classroom Lab Ideas

### Lab 1: Model Status Check

**Goal:** ให้นักศึกษาตรวจว่า OpenClaw เชื่อม provider ได้จริง

```bash
openclaw models status
openclaw models status --probe
```

Deliverable:

```text
Screenshot หรือข้อความ status โดยไม่เปิดเผย API Key
```

### Lab 2: Prompt Pattern

**Goal:** เขียน prompt ที่มี role, task, constraints และ output format

Deliverable:

```text
Prompt 1 ชุด + output summary ไม่เกิน 300 คำ
```

### Lab 3: Cron Brief

**Goal:** สร้าง Cron ที่จำกัด output และไม่ทำงานหนักเกินไป

Deliverable:

```text
Cron command + expected output format
```

### Lab 4: Security Review

**Goal:** ให้นักศึกษาตรวจ sample config ว่ามี secret หรือความเสี่ยงใด

Deliverable:

```text
Security checklist + recommended fix
```

---

## Command Cheat Sheet

### System

```bash
openclaw --version
openclaw doctor
openclaw gateway status
openclaw gateway restart
openclaw dashboard
```

### Models

```bash
openclaw models status
openclaw models status --probe
openclaw models list --provider openrouter
openclaw models scan
openclaw models set "openrouter/<provider>/<model-id>:free"
openclaw models fallbacks list
openclaw models fallbacks clear
openclaw models fallbacks add "openrouter/<provider>/<model-id>:free"
openclaw models aliases list
openclaw models aliases add or-free "openrouter/<provider>/<model-id>:free"
```

### Auth

```bash
openclaw models auth list
openclaw models auth login --provider openrouter
```

### Web Search

```bash
openclaw configure --section web
openclaw gateway restart
```

### Cron

```bash
openclaw cron list
openclaw cron run "<job-id>"
openclaw cron runs --id "<job-id>"
openclaw cron disable "<job-id>"
openclaw cron enable "<job-id>"
openclaw cron edit "<job-id>" --model "openrouter/<provider>/<model-id>:free"
```

### Logs

```bash
openclaw logs --help
openclaw logs --follow
```

---

## Recommended Repository Structure

```text
.
├── README.md
├── docs/
│   ├── openrouter-setup.md
│   ├── model-strategy.md
│   ├── cron-recipes.md
│   ├── troubleshooting.md
│   └── security-checklist.md
├── lessons/
│   └── openclaw_openai_gpt_5_x_lesson_th.md
├── examples/
│   ├── prompts/
│   ├── cron/
│   └── telegram/
└── LICENSE
```

> หาก repository ยังไม่มีโครงสร้างนี้ ให้ค่อย ๆ เพิ่มใน version branch ตาม workflow ของ project

---

## Maintenance Notes

ก่อนสอนหรือ demo ทุกครั้ง:

```text
[ ] ตรวจ OpenClaw version
[ ] ตรวจ Node.js version
[ ] ตรวจ OpenRouter API Key
[ ] ตรวจ available free models
[ ] ตรวจ model probe
[ ] ตรวจ Telegram channel / chat id
[ ] ตรวจ Cron jobs ที่เปิดอยู่
[ ] ตรวจว่าไม่มี secret ในไฟล์ตัวอย่าง
```

หลังสอน:

```text
[ ] ปิด Cron ที่ไม่ใช้
[ ] rotate API Key หากใช้เครื่องร่วม
[ ] ลบ log หรือ sample data ที่มีข้อมูลส่วนบุคคล
[ ] บันทึก known issues เพื่อปรับปรุงบทเรียนครั้งถัดไป
```

---

## External References

- OpenClaw Models CLI: https://docs.openclaw.ai/cli/models
- OpenClaw Model Providers: https://docs.openclaw.ai/concepts/model-providers
- OpenClaw OpenRouter Notes: https://docs.openclaw.ai/openrouter
- OpenRouter OpenClaw Integration: https://openrouter.ai/docs/cookbook/coding-agents/openclaw-integration
- OpenRouter Documentation: https://openrouter.ai/docs

---

## License

โปรดตรวจสอบไฟล์ `LICENSE` ของ repository นี้ก่อนนำไปเผยแพร่ ดัดแปลง หรือใช้ประกอบการอบรมเชิงพาณิชย์

หากยังไม่มีไฟล์ `LICENSE` แนะนำให้เพิ่ม license ให้ชัดเจน เช่น MIT License หรือ license ที่เจ้าของ repository กำหนด

---

## Summary

OpenClaw + OpenRouter Free Model เหมาะสำหรับการเรียนรู้และสาธิต AI Agent workflow เพราะช่วยให้เห็นภาพครบตั้งแต่ gateway, model provider, prompt, tools, cron automation, Telegram และ security hygiene

หลักปฏิบัติที่ควรยึดเสมอ:

```text
ตรวจสถานะก่อนสอน
ใช้ exact model ref
ยืนยัน free model ก่อน demo
จำกัด prompt/output
ไม่เปิดเผย secret
backup ก่อนแก้ config
ใช้ /new เมื่อ context ค้าง
ตรวจ logs หลังจบงาน
```
