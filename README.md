# OpenClaw + OpenRouter Free Model Installation & Operation Manual

> คู่มือภาษาไทยสำหรับติดตั้ง ใช้งาน สอน และแชร์การใช้งาน **OpenClaw** ร่วมกับ **OpenRouter Free Model**  
> เหมาะสำหรับ macOS เป็นหลัก และปรับใช้กับ Linux / WSL2 ได้

---

## สารบัญ

1. [ภาพรวมระบบ](#1-ภาพรวมระบบ)
2. [เตรียมเครื่องก่อนติดตั้ง](#2-เตรียมเครื่องก่อนติดตั้ง)
3. [ติดตั้ง OpenClaw](#3-ติดตั้ง-openclaw)
4. [เปิด Dashboard และตรวจ Gateway](#4-เปิด-dashboard-และตรวจ-gateway)
5. [สร้าง OpenRouter API Key](#5-สร้าง-openrouter-api-key)
6. [เชื่อม OpenClaw กับ OpenRouter](#6-เชื่อม-openclaw-กับ-openrouter)
7. [ตั้งค่า OpenRouter Free Model](#7-ตั้งค่า-openrouter-free-model)
8. [ทดสอบ Model](#8-ทดสอบ-model)
9. [ตั้งค่า Web Search](#9-ตั้งค่า-web-search)
10. [สร้าง Cron Job แบบประหยัด](#10-สร้าง-cron-job-แบบประหยัด)
11. [อ่านไฟล์ เขียนไฟล์ และสร้างโฟลเดอร์](#11-อ่านไฟล์-เขียนไฟล์-และสร้างโฟลเดอร์)
12. [ดูแล Cron Job](#12-ดูแล-cron-job)
13. [Telegram Recovery](#13-telegram-recovery)
14. [OpenRouter Cost Control](#14-openrouter-cost-control)
15. [Context Overflow Playbook](#15-context-overflow-playbook)
16. [Model Strategy](#16-model-strategy)
17. [AorAke Labour Law Counsel Agent](#17-aorake-labour-law-counsel-agent)
18. [Security & Token Hygiene](#18-security--token-hygiene)
19. [Rollback Plan](#19-rollback-plan)
20. [Troubleshooting](#20-troubleshooting)
21. [Workshop Checklist](#21-workshop-checklist)
22. [Command Cheat Sheet](#22-command-cheat-sheet)

---

## 1. ภาพรวมระบบ

OpenClaw คือระบบ AI Agent ที่รันบนเครื่องผู้ใช้ มี Gateway, Dashboard, Model Provider, Channel เช่น Telegram และระบบ Cron สำหรับงานอัตโนมัติ

```text
User / Telegram / Dashboard
        ↓
OpenClaw Gateway
        ↓
Agent Session
        ↓
Model Provider เช่น OpenRouter
        ↓
Tools / Files / Web / Cron / Excel
```

OpenRouter คือบริการรวมโมเดลหลายค่ายไว้หลัง API เดียว เช่น Qwen, Llama, DeepSeek, Gemini, GPT OSS และอื่น ๆ

### Free Model คืออะไร

| ประเภท | ความหมาย | เหมาะกับ |
|---|---|---|
| `openrouter/free` | ให้ OpenRouter เลือก free model ที่พร้อมใช้ | ทดลอง / สอน / งานเบา |
| `<model-id>:free` | เลือก free variant ของ model เฉพาะ | คุม model ได้มากขึ้น |
| `openrouter/auto` | OpenRouter เลือก model ให้ | สะดวก แต่มีโอกาสเสีย credit |

> ถ้าต้องการ **free-only** อย่าใช้ `openrouter/auto` เป็นค่าเริ่มต้น

---

## 2. เตรียมเครื่องก่อนติดตั้ง

ตรวจ macOS และ Node.js:

```bash
sw_vers
node --version
npm --version
```

ค่าที่แนะนำ:

```text
Node.js 24 recommended
หรือ Node.js 22.14+
```

ติดตั้ง Node.js ผ่าน Homebrew:

```bash
brew install node
```

ตรวจซ้ำ:

```bash
node --version
npm --version
```

---

## 3. ติดตั้ง OpenClaw

### วิธีที่ 1: Installer Script

```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

### วิธีที่ 2: npm

```bash
npm install -g openclaw@latest
openclaw onboard --install-daemon
```

ตรวจว่าติดตั้งสำเร็จ:

```bash
openclaw --version
openclaw doctor
openclaw gateway status
```

ผลที่ต้องการ:

```text
Gateway: running
Connectivity probe: ok
Dashboard: http://127.0.0.1:18789/
```

---

## 4. เปิด Dashboard และตรวจ Gateway

เปิด Dashboard:

```bash
openclaw dashboard
```

หรือ:

```bash
open http://127.0.0.1:18789
```

Restart Gateway:

```bash
openclaw gateway restart
```

> คำสั่ง `openclaw restart` ไม่ถูกต้อง ให้ใช้ `openclaw gateway restart`

ตรวจสุขภาพระบบ:

```bash
openclaw gateway status
openclaw models status
openclaw models status --probe
openclaw cron list
```

---

## 5. สร้าง OpenRouter API Key

ขั้นตอน:

1. เข้า OpenRouter
2. Login
3. ไปที่ **API Keys**
4. กด **Create Key**
5. ตั้งชื่อ เช่น `OpenClaw-Teaching-Free-Model`
6. Copy key เก็บใน password manager

รูปแบบ key ที่ถูกต้อง:

```text
sk-or-v1-...
```

ไม่ใช่:

```text
hf_...        # Hugging Face
sk-proj-...   # OpenAI
```

### ห้ามส่งข้อมูลเหล่านี้ในแชต

```text
API key
Gateway token
Telegram bot token
Password
Session token
.env
auth-profiles.json
```

---

## 6. เชื่อม OpenClaw กับ OpenRouter

Login OpenRouter:

```bash
openclaw models auth login --provider openrouter
```

ถ้าใช้ไม่ได้ ให้ใช้ onboarding:

```bash
openclaw onboard --auth-choice openrouter-api-key
```

ตรวจสถานะ:

```bash
openclaw models status
openclaw models status --probe
```

---

## 7. ตั้งค่า OpenRouter Free Model

### ตั้ง Default เป็น Free Router

```bash
openclaw models set openrouter/free
```

ถ้า model ref ไม่ตรง ให้ตรวจรายการก่อน:

```bash
openclaw models list --provider openrouter
```

บาง build อาจใช้:

```bash
openclaw models set openrouter/openrouter/free
```

### ตั้ง fallback เป็น Free Router

```bash
openclaw models fallbacks clear
openclaw models fallbacks add openrouter/free
```

หรือถ้า exact ref เป็นอีกแบบ:

```bash
openclaw models fallbacks clear
openclaw models fallbacks add openrouter/openrouter/free
```

เพิ่ม alias:

```bash
openclaw models aliases add or-free openrouter/free
```

Restart และ Probe:

```bash
openclaw gateway restart
openclaw models status --probe
```

---

## 8. ทดสอบ Model

ทดสอบผ่าน CLI:

```bash
openclaw models status --probe
```

ทดสอบผ่าน Dashboard:

```bash
openclaw dashboard
```

ส่งข้อความสั้น ๆ:

```text
สวัสดี ช่วยตอบสั้น ๆ ว่าระบบพร้อมใช้งานหรือไม่
```

ทดสอบผ่าน Telegram:

```text
/new
ตรวจสถานะสั้น ๆ
```

---

## 9. ตั้งค่า Web Search

ถ้าเจอ error:

```text
web_search is disabled or no provider is available
```

ให้ตั้งค่า web provider:

```bash
openclaw configure --section web
```

Provider ที่แนะนำ:

| Provider | จุดเด่น | เหมาะกับ |
|---|---|---|
| DuckDuckGo | ไม่ใช้ API key | ทดสอบเร็ว |
| Brave | API-based / เสถียรกว่า | ใช้งานจริง |
| Gemini Search | grounding/citation | สรุปพร้อมแหล่งอ้างอิง |
| SearXNG | self-host/privacy | ผู้ใช้ขั้นสูง |

หลังตั้งค่า:

```bash
openclaw gateway restart
```

---

## 10. สร้าง Cron Job แบบประหยัด

หลักการสำหรับ Free Model:

```text
จำกัดผลลัพธ์ไม่เกิน 3 รายการ
ไม่อ่าน PDF เต็ม
ไม่ทำวิเคราะห์เต็ม
ตอบไม่เกิน 600–900 คำ
ถามก่อนทำงานหนักต่อ
```

ตัวอย่าง Daily Ultra-light Brief:

```bash
MSG=$(cat <<'EOF'
ทำ Daily Legal Discovery แบบ Ultra-light

ค้นเฉพาะรายการสำคัญช่วง 7 วันล่าสุด
จำกัดไม่เกิน 3 รายการ
ตอบเป็นภาษาไทย กระชับ ไม่เกิน 700 คำ
ห้ามอ่าน PDF เต็ม
ห้ามทำบทวิเคราะห์ยาว
ถ้าไม่พบ ให้ตอบว่า “วันนี้ไม่พบรายการสำคัญที่เข้าเกณฑ์”

รูปแบบ:
1) สถานะรวม
2) รายการสำคัญ
3) ผลกระทบต่อ HR/Compliance แบบสั้น
4) แหล่งอ้างอิงถ้ามี
EOF
)

openclaw cron add \
  --name "daily-ultra-light-legal-brief" \
  --cron "0 8 * * *" \
  --tz "Asia/Bangkok" \
  --session isolated \
  --announce \
  --channel telegram \
  --to "8757711459" \
  --model openrouter/free \
  --message "$MSG"
```

ตรวจ Cron:

```bash
openclaw cron list
```

Run test:

```bash
openclaw cron run "<job-id>"
openclaw cron runs --id "<job-id>"
```

---

## 11. อ่านไฟล์ เขียนไฟล์ และสร้างโฟลเดอร์

### หลักความปลอดภัย

```text
อ่านก่อน → ตรวจ path → backup ถ้าจะแก้ไฟล์ → เขียนแบบจำกัดขอบเขต → ตรวจผลทันที
```

### Workspace

```text
~/.openclaw/workspace
~/Legal-Knowledge
```

เปิด workspace:

```bash
open "$HOME/.openclaw/workspace"
```

สร้างโฟลเดอร์:

```bash
mkdir -p "$HOME/Legal-Knowledge/01_Labour_Law"
mkdir -p "$HOME/Legal-Knowledge/output/labour_law_register"
mkdir -p "$HOME/Legal-Knowledge/output/case_law_log"
```

อ่านไฟล์:

```bash
cat "$HOME/Legal-Knowledge/INDEX.md"
head -80 "$HOME/Legal-Knowledge/INDEX.md"
tail -80 "$HOME/Legal-Knowledge/INDEX.md"
```

ค้นหาในไฟล์:

```bash
grep -n "ค่าชดเชย" "$HOME/Legal-Knowledge/01_Labour_Law/notes.md"
grep -Rni "เลิกจ้าง" "$HOME/Legal-Knowledge/01_Labour_Law"
```

อ่าน DOCX บน macOS:

```bash
textutil -convert txt -stdout "$HOME/Legal-Knowledge/01_Labour_Law/example.docx" | head -80
```

ค้นหาไฟล์:

```bash
find "$HOME/Legal-Knowledge" -maxdepth 3 -type f -print
find "$HOME/Legal-Knowledge" -maxdepth 3 -type f -name "*.md" -print
find "$HOME/Legal-Knowledge" -maxdepth 5 -type f -name "*.xlsx" -print
```

เขียนไฟล์ใหม่:

```bash
cat <<'EOF' > "$HOME/Legal-Knowledge/INDEX.md"
# Legal-Knowledge Index

- 01_Labour_Law: กฎหมายแรงงาน
- 02_Civil_Commercial_Code: ป.พ.พ.
- 09_Case_Law: คำพิพากษา
EOF
```

Append ไฟล์:

```bash
cat <<'EOF' >> "$HOME/Legal-Knowledge/INDEX.md"

## Updated Notes
- เพิ่มรายการใหม่วันนี้
EOF
```

Backup ก่อนเขียนทับ:

```bash
cp "$HOME/Legal-Knowledge/INDEX.md" \
   "$HOME/Legal-Knowledge/INDEX.backup.$(date +%Y%m%d-%H%M%S).md"
```

สร้าง CSV fallback:

```bash
cat <<'EOF' > "$HOME/Legal-Knowledge/output/labour_law_register/labour_law_register.csv"
Run_Month,Legal_Title,Legal_Type,Source_Agency,Published_Date,Effective_Date,Key_Summary,Source_URL
2026-05,No significant update,Monthly Log,System,,,,
EOF
```

สร้าง symlink:

```bash
ln -s "$HOME/Legal-Knowledge" "$HOME/.openclaw/workspace/Legal-Knowledge"
ls -la "$HOME/.openclaw/workspace" | grep Legal
```

### คำสั่งที่ควรระวังสูง

| คำสั่ง | ความเสี่ยง |
|---|---|
| `rm` | ลบไฟล์ |
| `rm -rf` | ลบทั้งโฟลเดอร์ |
| `>` | เขียนทับไฟล์ |
| `mv` | ย้าย/ทับไฟล์ |
| `chmod` | เปลี่ยนสิทธิ์ |
| `sudo` | สิทธิ์สูง |

---

## 12. ดูแล Cron Job

คำสั่งหลัก:

```bash
openclaw cron list
openclaw cron run "<job-id>"
openclaw cron runs --id "<job-id>"
openclaw cron edit "<job-id>" --model openrouter/free
openclaw cron disable "<job-id>"
openclaw cron enable "<job-id>"
```

ควร disable เมื่อพบ:

```text
error ซ้ำหลายครั้ง
billing error
context overflow
web_search disabled
Telegram target ผิด
job อยู่ใน backoff
```

---

## 13. Telegram Recovery

ถ้าเจอ:

```text
Something went wrong while processing your request.
Please try again, or use /new to start a fresh session.
```

ใน Telegram ให้ส่ง:

```text
/new
```

จากนั้นทดสอบ:

```text
ตรวจสถานะสั้น ๆ
```

ใช้ `/new` เมื่อ:

```text
session ค้าง
context overflow
model เปลี่ยนใหม่
agent ตอบไม่จบ
tool error ต่อเนื่อง
คุยยาวมากแล้ว
```

---

## 14. OpenRouter Cost Control

### Free-only vs Auto

| Model Ref | ความหมาย | ความเสี่ยงค่าใช้จ่าย |
|---|---|---|
| `openrouter/free` | free router | ต่ำสุด |
| `<model-id>:free` | free variant | ต่ำ |
| `openrouter/auto` | auto route | อาจเสียเงิน |
| paid model | model คิดเงิน | เสียเงินตาม usage |

Error 402:

```text
402 This request requires more credits, or fewer max_tokens.
```

วิธีแก้:

```text
1) เติม credit
2) ลด prompt/output
3) เปลี่ยนเป็น openrouter/free
4) ปิด cron ที่กิน token
```

---

## 15. Context Overflow Playbook

เกิดจาก:

```text
prompt + tool input + chat history + output ที่ขอ รวมกันเกิน context limit ของ model
```

วิธีแก้:

```text
ใช้ /new ใน Telegram
ลด prompt
ลด output ไม่เกิน 700–900 คำ
จำกัดผลลัพธ์ไม่เกิน 3 รายการ
ไม่อ่าน PDF เต็มใน cron
แยก Discovery กับ Analysis
```

Ultra-light Prompt Pattern:

```text
ค้นเฉพาะ 7 วันล่าสุด
จำกัดไม่เกิน 3 รายการ
ห้ามอ่าน PDF เต็ม
ห้ามทำบทวิเคราะห์เต็ม
ตอบไม่เกิน 700 คำ
ถามท้ายว่า ต้องการให้วิเคราะห์ต่อหรือไม่
```

---

## 16. Model Strategy

| งาน | Model Strategy |
|---|---|
| ติดตั้ง/ทดสอบ | `openrouter/free` |
| Cron เบา | `openrouter/free` หรือ free model เฉพาะ |
| Chat ทั่วไป | `openrouter/auto` เมื่อมี credit |
| วิเคราะห์กฎหมาย | model context ใหญ่ / paid model |
| อ่าน PDF ยาว | model context ใหญ่ + แบ่งงานเป็นช่วง |
| Excel append | prompt สั้น / tool-friendly |
| งาน private/offline | Ollama local |

แนวคิด 3 Mode:

```text
Discovery Mode: ค้นเบา / free model
Counsel Mode: วิเคราะห์ลึก / context ใหญ่
Record Mode: บันทึกฐานข้อมูล / prompt สั้น / append-only
```

---

## 17. AorAke Labour Law Counsel Agent

Role:

```text
AorAke Labour Law Counsel Agent
```

Mission:

```text
ช่วยวิเคราะห์กฎหมายแรงงาน คำพิพากษา ข้อพิพาทแรงงาน HR Compliance และ Audit
โดยยึดความน่าเชื่อถือ ความถูกต้อง และการใช้งานจริง
```

Workflow:

```text
ค้นหา → วิเคราะห์ → ประเมินความเสี่ยง → เสนอแนวทาง → ถามอนุมัติ → บันทึกฐานความรู้
```

Guardrails:

```text
ไม่เดากฎหมาย
ไม่สร้างเลขฎีกาเอง
ไม่อ้างแหล่งที่ไม่มีจริง
ใช้แหล่งทางการก่อน
ถ้า web_search ใช้ไม่ได้ ต้องแจ้งข้อจำกัด
ถามก่อนบันทึก Excel
append-only
ตรวจ duplicate ก่อนบันทึก
```

รูปแบบวิเคราะห์กฎหมายแรงงาน:

```text
1) ข้อเท็จจริง
2) ประเด็นกฎหมาย
3) กฎหมายที่เกี่ยวข้อง
4) วิเคราะห์
5) ความเสี่ยง
6) ทางเลือก
7) เอกสารที่ควรเตรียม
8) ข้อเสนอแนะ
```

IRLAC+:

```text
I = Issue
R = Rule
L = Legal Source
A = Application
C = Conclusion
+ = HR / Compliance / Audit Impact
```

---

## 18. Security & Token Hygiene

ห้ามเปิดเผย:

```text
API key
Telegram bot token
Gateway token
OAuth token
Password
.env
auth-profiles.json
```

ค้นหา secret ใน workspace/log:

```bash
grep -RniE "sk-|api[_-]?key|token|secret|password" "$HOME/.openclaw" --exclude-dir=node_modules
```

ถ้าเจอ warning:

```text
gateway.controlUi.allowInsecureAuth=true
```

ตรวจด้วย:

```bash
openclaw security audit
```

---

## 19. Rollback Plan

Backup config ก่อนแก้:

```bash
cp "$HOME/.openclaw/openclaw.json" \
   "$HOME/.openclaw/openclaw.backup.$(date +%Y%m%d-%H%M%S).json"
```

หา backup ล่าสุด:

```bash
ls -lt "$HOME/.openclaw" | grep "openclaw.backup"
```

Restore:

```bash
cp "$HOME/.openclaw/openclaw.backup.YYYYMMDD-HHMMSS.json" \
   "$HOME/.openclaw/openclaw.json"

openclaw gateway restart
openclaw models status --probe
```

Rollback model เป็น Free:

```bash
openclaw models set openrouter/free
openclaw models fallbacks clear
openclaw models fallbacks add openrouter/free
openclaw gateway restart
openclaw models status --probe
```

---

## 20. Troubleshooting

| Error | สาเหตุ | วิธีแก้ |
|---|---|---|
| `unknown command restart` | ใช้คำสั่งผิด | `openclaw gateway restart` |
| `401 Missing Authentication header` | API key ผิด/หาย | login OpenRouter ใหม่ |
| `402 Requires more credits` | credit ไม่พอ | ใช้ free model หรือเติม credit |
| `Context overflow` | prompt/tool/output ใหญ่เกิน | ลด prompt/output, ใช้ `/new` |
| `No endpoints found` | model route ใช้ไม่ได้ | ตรวจ `models list` |
| `web_search disabled` | ยังไม่ตั้ง web provider | `openclaw configure --section web` |
| `logs --tail` ไม่รองรับ | CLI version ไม่รับ flag | ใช้ `openclaw logs --help` |

---

## 21. Workshop Checklist

Installation Tick List:

```text
[ ] Node.js พร้อม
[ ] OpenClaw ติดตั้งแล้ว
[ ] Gateway running
[ ] Dashboard เปิดได้
[ ] OpenRouter API key login แล้ว
[ ] Free model ตั้งค่าแล้ว
[ ] Probe ผ่าน
[ ] Telegram pairing ผ่าน
[ ] Web search provider เปิดแล้ว
[ ] Cron test ผ่าน
```

Troubleshooting Tick List:

```text
[ ] ดู cron list
[ ] ดู cron runs
[ ] ตรวจ model probe
[ ] ตรวจ credit OpenRouter
[ ] ตรวจ web_search
[ ] ตรวจ Telegram target
[ ] ใช้ /new ถ้า session ค้าง
[ ] disable job ที่ error ซ้ำ
[ ] backup ก่อนแก้ config
```

---

## 22. Command Cheat Sheet

```bash
# ตรวจระบบ
openclaw doctor
openclaw gateway status
openclaw models status
openclaw models status --probe
openclaw cron list

# Restart gateway
openclaw gateway restart

# Dashboard
openclaw dashboard
open http://127.0.0.1:18789

# OpenRouter auth
openclaw models auth login --provider openrouter

# ตั้ง free model
openclaw models set openrouter/free
openclaw models fallbacks clear
openclaw models fallbacks add openrouter/free

# ถ้า model ref ไม่ตรง
openclaw models list --provider openrouter

# Cron
openclaw cron list
openclaw cron run "<job-id>"
openclaw cron runs --id "<job-id>"
openclaw cron disable "<job-id>"
openclaw cron enable "<job-id>"

# Logs
openclaw logs --help
openclaw logs --follow

# Web search
openclaw configure --section web
openclaw gateway restart
```

---

## บทสรุป

การใช้ OpenClaw กับ OpenRouter Free Model เหมาะสำหรับการเรียน ทดลอง สอน และทำงานเบา เช่น Telegram brief, candidate discovery, classification และ prompt prototype

ถ้าจะใช้งานจริงด้านกฎหมายหรือ HR Compliance ควรออกแบบเป็น 3 ชั้น:

```text
1) Discovery Mode: openrouter/free
2) Counsel Mode: model ที่มี context ใหญ่และมี credit เพียงพอ
3) Record Mode: append-only หลังอนุมัติ
```

หัวใจสำคัญ:

```text
ใช้ free model ให้ถูกงาน
ไม่ใช้ openrouter/auto ถ้าต้องการ free-only
ลด prompt
จำกัด output
ตรวจ logs
ไม่เปิดเผย secret
backup ก่อนแก้ config
```
