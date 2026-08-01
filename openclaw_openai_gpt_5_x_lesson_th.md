# บทเรียน: OpenClaw + OpenAI GPT-5.x สำหรับวิทยากรไอที

> เอกสารนี้ออกแบบเป็นบทเรียนภาษาไทยสำหรับ **วิทยากรด้านไอทีที่มีประสบการณ์** เพื่อใช้นำเสนอแก่นิสิต/นักศึกษา โดยเน้นการเข้าใจภาพรวมระบบ AI Agent, การเชื่อมต่อ OpenClaw กับ OpenAI GPT-5.x, การใช้ OpenRouter เป็นทางเลือก, การออกแบบ prompt, การจัดการเครื่องมือ, ความปลอดภัย และการควบคุมต้นทุน

---

## 0. Metadata

| รายการ | รายละเอียด |
|---|---|
| ชื่อบทเรียน | OpenClaw + OpenAI GPT-5.x for Practical AI Agent Operation |
| กลุ่มเป้าหมาย | นิสิต/นักศึกษาด้าน IT, CS, Software Engineering, Digital Business, AI Application |
| ผู้สอน | วิทยากรด้านไอทีที่มีประสบการณ์ด้าน API, CLI, Web App, Automation หรือ AI Tools |
| ระยะเวลาแนะนำ | 2.5–3 ชั่วโมง หรือแบ่งเป็น 2 คาบเรียน |
| รูปแบบ | Lecture + Demo + Lab + Discussion |
| ภาษา | ไทย พร้อมศัพท์เทคนิคภาษาอังกฤษ |
| ระดับ | Intermediate to Advanced Beginner |
| อัปเดตจากแหล่งข้อมูล | 1 สิงหาคม 2026 |

---

## 1. วัตถุประสงค์การเรียนรู้

เมื่อเรียนจบบทนี้ ผู้เรียนควรสามารถ:

1. อธิบายสถาปัตยกรรมของ OpenClaw ในฐานะ AI Agent Gateway ได้
2. แยกความแตกต่างระหว่าง Model Provider, Model Ref, Runtime, Tool และ Channel ได้
3. ตั้งค่า OpenClaw ให้เชื่อมต่อ OpenAI หรือ OpenRouter ในระดับ concept ได้
4. เลือกใช้ GPT-5.x model ตามงาน เช่น reasoning, coding, cost-sensitive workload และ automation ได้
5. ออกแบบ Prompt Pattern สำหรับงานสรุป ค้นเว็บ อ่านไฟล์ และจัดหมวดข้อมูลได้
6. อธิบายความเสี่ยงด้าน API Key, token, cost, rate limit, context overflow และ tool permission ได้
7. ออกแบบ lab หรือ assignment สำหรับนักศึกษาโดยไม่เปิดเผย secret จริง

---

## 2. Executive Summary สำหรับผู้สอน

OpenClaw เป็นแพลตฟอร์ม AI Agent แบบ open-source ที่ทำหน้าที่เชื่อมผู้ใช้เข้ากับโมเดลและช่องทางสื่อสารหลายแบบ เช่น Telegram, Discord, Slack, Signal, iMessage และ WhatsApp รวมถึงรองรับ LLM providers หลายรายผ่านการตั้งค่า model/provider ที่ยืดหยุ่น [OpenRouter OpenClaw Integration, 2026]

ฝั่ง OpenAI ควรสอนผ่านแนวคิดสมัยใหม่ของ **Responses API**, **Function Calling**, **Structured Outputs**, และ built-in tools เช่น Web Search, File Search และ Computer Use เพราะแนวคิดเหล่านี้เป็นฐานของ agent workflow ใน API สมัยใหม่ [OpenAI Help Center, 2026]

ฝั่ง OpenClaw ควรเน้นว่า model reference หรือ `provider/model` ไม่ใช่แค่ชื่อโมเดล แต่เป็นวิธีระบุเส้นทางการเรียก provider เช่น `openai/gpt-5.6-sol` หรือ `openrouter/<provider>/<model>` โดย OpenClaw docs ระบุว่าหาก model ID แบบ OpenRouter มี `/` อยู่ภายใน ต้องใส่ provider prefix ให้ครบ เช่น `openrouter/moonshotai/kimi-k2` [OpenClaw Models CLI, 2026]

---

## 3. Concept Map: OpenClaw + OpenAI + OpenRouter

```text
Student / User
   ↓
Interface Layer
- Telegram
- Dashboard
- CLI
   ↓
OpenClaw Gateway
   ↓
Agent Session
   ↓
Model Provider Layer
- OpenAI GPT-5.x
- OpenRouter
- Other providers
   ↓
Tool Layer
- Web Search
- File Search
- Function Calling
- Cron / Automation
- Local Files
   ↓
Output
- Summary
- Report
- Action Item
- Teaching Demo
```

### Teaching Note

ให้ผู้สอนเน้นว่า OpenClaw ไม่ใช่ “โมเดล AI” โดยตรง แต่เป็น **agent orchestration layer** หรือระบบประสานงานระหว่างผู้ใช้ โมเดล เครื่องมือ และช่องทางสื่อสาร

---

## 4. คำศัพท์หลักที่นิสิตต้องเข้าใจ

| Term | ความหมายแบบสั้น | ตัวอย่าง |
|---|---|---|
| Agent | ระบบที่รับเป้าหมาย แล้ววางแผน/ใช้เครื่องมือเพื่อตอบสนอง | AI ช่วยสรุปข่าวรายวัน |
| Gateway | จุดกลางที่รับคำสั่งและส่งต่อไปยัง agent/model/tool | OpenClaw Gateway |
| Model Provider | ผู้ให้บริการโมเดล | OpenAI, OpenRouter |
| Model Ref | รูปแบบอ้างอิงโมเดล | `openai/gpt-5.6-sol` |
| Tool | ความสามารถเสริมที่ agent ใช้ได้ | Web Search, File Search, Function Calling |
| Channel | ช่องทางคุยกับ agent | Telegram, Dashboard |
| Runtime | สภาพแวดล้อมที่ agent ใช้เรียก model/tool | OpenClaw runtime, Codex-compatible runtime |
| Context Window | ขนาดข้อมูลที่โมเดลรับได้ในหนึ่งงาน | prompt + history + files + output budget |
| Rate Limit | ข้อจำกัดการเรียกใช้ API | requests/minute, tokens/minute |
| Secret | ข้อมูลลับ เช่น API Key | `OPENAI_API_KEY`, `OPENROUTER_API_KEY` |

---

## 5. Model Strategy: GPT-5.x สำหรับงานสอน

จากเอกสาร OpenAI API Models ล่าสุด OpenAI แนะนำให้เลือกตระกูล GPT-5.6 ตามวัตถุประสงค์งาน ได้แก่ Sol สำหรับ reasoning/coding ที่ซับซ้อน, Terra สำหรับสมดุลระหว่างความฉลาดกับต้นทุน และ Luna สำหรับงานปริมาณมากที่ต้องประหยัดต้นทุน [OpenAI Models, 2026]

| งานสอน | Model Strategy | เหตุผล |
|---|---|---|
| วิเคราะห์โจทย์ซับซ้อน | GPT-5.x reasoning/high-capability | ต้องการ reasoning และ coding คุณภาพสูง |
| สรุปเอกสาร | GPT-5.x balanced | ต้องการคุณภาพและต้นทุนสมดุล |
| งาน cron รายวัน | GPT-5.x cost-sensitive หรือ OpenRouter free model | ลดค่าใช้จ่ายและ token |
| classification เบื้องต้น | lightweight model | ไม่ต้องใช้โมเดลใหญ่ทุกครั้ง |
| coding demo | GPT-5.x coding/reasoning | เหมาะกับการอธิบายโค้ดและแก้ bug |

### ตัวอย่าง Model Ref เชิงแนวคิด

```bash
# ตรวจ model ที่ provider รองรับจริงก่อนใช้งาน
openclaw models list --provider openai
openclaw models list --provider openrouter

# ตัวอย่างเชิงแนวคิด: ตั้ง OpenAI เป็น primary
openclaw models set openai/gpt-5.6-sol

# ตัวอย่างเชิงแนวคิด: ใช้ OpenRouter free model
openclaw models set openrouter/free
```

> หมายเหตุสำหรับผู้สอน: model name และสิทธิ์การเข้าถึงเปลี่ยนได้ตามบัญชี/แผนบริการ/API policy จึงควรสอนให้นักศึกษาตรวจสอบด้วย `openclaw models list --provider <id>` ก่อนใช้จริง

---

## 6. OpenAI API Concepts ที่ควรสอน

### 6.1 Responses API

Responses API เป็นแนวคิดกลางสำหรับการสร้าง agent workflow สมัยใหม่ เพราะรวมความสามารถที่เดิมกระจายอยู่ระหว่าง Chat Completions และ Assistants API และรองรับ function calling ใน workflow เดียวกัน [OpenAI Help Center, 2026]

### 6.2 Function Calling

Function Calling คือวิธีให้โมเดลเชื่อมต่อกับระบบภายนอก เช่น database, API, tool, calculation หรือ internal service โดยโมเดลสร้าง argument เพื่อเรียก function ที่ผู้พัฒนากำหนด [OpenAI Help Center, 2026]

ตัวอย่าง use case ในห้องเรียน:

```text
ผู้ใช้ถาม: “สรุปยอดขายวันนี้และแจ้งเตือนถ้าต่ำกว่าเป้า”
Agent ทำงาน:
1. เรียก function get_sales_today()
2. วิเคราะห์ยอดขายเทียบ target
3. เรียก function send_telegram_alert() ถ้าต่ำกว่าเกณฑ์
4. สรุปผลเป็นภาษาไทย
```

### 6.3 Structured Outputs

Structured Outputs ช่วยให้ function-call arguments ตรงตาม JSON Schema เมื่อกำหนด `strict: true` ใน function definition บน model/request configuration ที่รองรับ [OpenAI Help Center, 2026]

ตัวอย่างสำหรับสอนแนวคิด:

```json
{
  "task_type": "document_summary",
  "priority": "normal",
  "language": "th",
  "required_sections": ["summary", "risks", "action_items"]
}
```

### 6.4 Built-in Tools

OpenAI ระบุว่า model รุ่นล่าสุดรองรับ tools เช่น Functions, Web Search, File Search และ Computer Use โดยมีการคิดค่าบริการตาม model และบาง tool-specific call [OpenAI Models, 2026]

> Teaching Point: การใช้ tool ทำให้ agent เก่งขึ้น แต่เพิ่มความเสี่ยงเรื่อง cost, permission, reliability และ governance

---

## 7. OpenClaw Model Provider และ Routing

OpenClaw ใช้แนวคิด model ref แบบ `provider/model` เพื่อระบุ provider และ model โดยเอกสาร OpenClaw อธิบายว่า prefix เช่น `openai/<model>` ใช้เลือก canonical OpenAI provider และไม่ควรตีความว่า prefix เดียวกันหมายถึง runtime เดียวกันเสมอไป [OpenClaw Model Providers, 2026]

### ตัวอย่าง

```bash
# OpenAI provider
openclaw models set openai/gpt-5.6-sol

# OpenRouter provider ที่ model id มี path หลายชั้น
openclaw models set openrouter/moonshotai/kimi-k2

# Free routing ของ OpenRouter ตามที่ repo นี้ใช้เป็นแนวทางสอน
openclaw models set openrouter/free
```

### ประเด็นที่ควรเน้นในห้องเรียน

1. `openai/<model>` = เลือก OpenAI provider/model
2. `openrouter/<provider>/<model>` = ผ่าน OpenRouter แล้วเลือก provider/model ด้านหลัง
3. `openrouter/free` = แนวคิด free model routing ที่เหมาะกับการทดลอง แต่ต้องตรวจ availability เสมอ
4. `openrouter/auto` = สะดวก แต่ควรระวังค่าใช้จ่าย เพราะอาจเลือก model ที่ไม่ใช่ free-only
5. หาก provider/model เปลี่ยนหรือ model ถูก deprecate ต้องมี fallback และ troubleshooting playbook

---

## 8. Installation / Onboarding Script สำหรับ Demo

### 8.1 OpenRouter Setup Wizard

OpenRouter documentation แนะนำให้ใช้ OpenClaw setup wizard ด้วยคำสั่ง `openclaw onboard` เพื่อเลือก OpenRouter, ใส่ API key, เลือก model และตั้งค่า messaging channel [OpenRouter OpenClaw Integration, 2026]

```bash
openclaw onboard
```

### 8.2 OpenRouter Quick Start แบบ CLI

```bash
export OPENROUTER_API_KEY="<your-openrouter-api-key>"

openclaw onboard \
  --auth-choice apiKey \
  --token-provider openrouter \
  --token "$OPENROUTER_API_KEY"
```

### 8.3 OpenAI Setup เชิงแนวคิด

```bash
# Login / Auth กับ OpenAI provider
openclaw models auth login --provider openai

# ตรวจสถานะ provider/model
openclaw models status
openclaw models status --probe

# ตั้ง model หลัก
openclaw models set openai/gpt-5.6-sol
```

> สำหรับห้องเรียน: ห้ามให้นักศึกษาส่ง API key ใน chat, Google Form, GitHub issue, screenshot หรือเอกสารส่งงาน

---

## 9. Lab 1: ตรวจระบบและ Model Provider

### Objective

ให้นักศึกษาเข้าใจการตรวจสถานะก่อนใช้งาน agent

### Commands

```bash
openclaw --version
openclaw doctor
openclaw gateway status
openclaw models status
openclaw models list --provider openai
openclaw models list --provider openrouter
```

### Expected Learning

นักศึกษาควรตอบได้ว่า:

- Gateway ทำงานหรือไม่
- Provider เชื่อมต่อแล้วหรือยัง
- มี model ใดให้ใช้จริงในบัญชีของตน
- model ref ที่พิมพ์ถูกต้องหรือไม่

---

## 10. Lab 2: Prompt Pattern สำหรับ AI Agent

### Template

```text
บทบาท:
คุณคือ AI Agent สำหรับ...

งาน:
ให้ทำอะไรอย่างชัดเจน

ข้อมูล:
ให้ใช้ข้อมูลใด

ข้อจำกัด:
ห้ามทำอะไร / จำกัดความยาว / ต้องอ้างอิงอะไร

รูปแบบผลลัพธ์:
ตาราง / bullet / JSON / Markdown

เกณฑ์ตรวจสอบ:
หากข้อมูลไม่พอ ให้ระบุว่า “ข้อมูลไม่เพียงพอ”
```

### Example: Daily News Brief

```text
บทบาท:
คุณคือ AI Agent ช่วยสรุปข่าวสำหรับนักศึกษา IT

งาน:
สรุปข่าวเทคโนโลยีสำคัญใน 24 ชั่วโมงล่าสุด

ข้อจำกัด:
- ไม่เกิน 3 ข่าว
- ตอบภาษาไทย
- ระบุแหล่งข่าวหรือวันที่ถ้ามี
- ห้ามคาดเดาถ้าไม่มีข้อมูล

รูปแบบผลลัพธ์:
1. หัวข้อข่าว
2. สรุปไม่เกิน 4 บรรทัด
3. ประเด็นที่นิสิตควรเรียนรู้
```

---

## 11. Lab 3: Cron Automation แบบประหยัด

### Use Case

สร้างงานสรุปข่าวรายวันหรือเตือนความจำสำหรับการเรียน

```bash
MSG=$(cat <<'EOF'
ทำ Daily IT Learning Brief ภาษาไทย
- สรุปไม่เกิน 3 ประเด็น
- เน้น AI, API, Software Engineering, Cybersecurity
- ระบุสิ่งที่นิสิตควรนำไปทดลองต่อ
- ถ้าข้อมูลไม่เพียงพอ ให้บอกว่า “ข้อมูลไม่เพียงพอ”
EOF
)

openclaw cron add \
  --name "daily-it-learning-brief" \
  --cron "0 8 * * *" \
  --tz "Asia/Bangkok" \
  --session isolated \
  --announce \
  --channel telegram \
  --to "<telegram-chat-id>" \
  --model openrouter/free \
  --message "$MSG"
```

### Debrief Questions

1. ทำไม cron ควรใช้ session แบบ isolated?
2. ถ้าใช้ model แพงใน cron รายวัน จะเกิดความเสี่ยงอะไร?
3. ถ้า agent ตอบยาวเกินไป จะควบคุมอย่างไร?
4. ถ้า model free unavailable ต้อง fallback อย่างไร?

---

## 12. Lab 4: File Workflow แบบปลอดภัย

### Folder Setup

```bash
mkdir -p "$HOME/AI-Agent-Lab/input"
mkdir -p "$HOME/AI-Agent-Lab/output"
mkdir -p "$HOME/AI-Agent-Lab/backup"
```

### Safe File Reading

```bash
head -80 "$HOME/AI-Agent-Lab/input/sample.txt"
```

### Safe Output Writing

```bash
cat <<'EOF' > "$HOME/AI-Agent-Lab/output/summary.md"
# Summary

- Key point 1
- Key point 2
- Action item
EOF
```

### Backup Before Edit

```bash
cp "$HOME/AI-Agent-Lab/output/summary.md" \
   "$HOME/AI-Agent-Lab/backup/summary.$(date +%Y%m%d-%H%M%S).md"
```

---

## 13. Security & Governance สำหรับห้องเรียน

### 13.1 Secret Hygiene

```text
[ ] ไม่ commit API key ลง GitHub
[ ] ไม่ส่ง API key ในแชต
[ ] ไม่ถ่าย screenshot ที่มี token
[ ] ใช้ .env และ .gitignore
[ ] แยก key สำหรับ demo/lab
[ ] ตั้ง spending limit ใน provider dashboard ถ้าทำได้
```

### 13.2 Tool Permission

ผู้สอนควรอธิบายว่า tool-enabled agent มีความเสี่ยงมากกว่า chatbot ทั่วไป เพราะ agent อาจอ่านไฟล์ เรียก API ส่งข้อความ หรือรัน automation ได้ จึงต้องออกแบบ permission แบบ least privilege

### 13.3 Cost Governance

```text
[ ] ใช้ lightweight model สำหรับงานซ้ำ
[ ] จำกัด output length
[ ] หลีกเลี่ยงการอ่านไฟล์ยาวทั้งฉบับ
[ ] ไม่ตั้ง cron ถี่เกินจำเป็น
[ ] ตรวจ usage dashboard ของ provider
[ ] แยก demo key จาก production key
```

---

## 14. Troubleshooting Playbook

| อาการ | สาเหตุที่เป็นไปได้ | วิธีตรวจ | วิธีแก้ |
|---|---|---|---|
| `401 Unauthorized` | API key ผิดหรือหมดอายุ | `openclaw models status --probe` | login provider ใหม่ |
| `402 Payment Required` | credit ไม่พอ | provider dashboard | เติม credit / ลด token / เปลี่ยน model |
| `rate limit` | request หรือ token เกิน limit | logs / provider dashboard | รอ, ลด prompt, ลด tool call |
| `context overflow` | prompt + file + history ใหญ่เกิน | ตรวจ input size | แยกงาน / ใช้ summary / ลดไฟล์ |
| model not found | model ref ผิดหรือถูก deprecate | `openclaw models list --provider <id>` | เลือก model ใหม่ |
| gateway ไม่ตอบ | gateway down หรือ session ค้าง | `openclaw gateway status` | restart gateway / เปิด session ใหม่ |
| cron ไม่รัน | cron expression หรือ timezone ผิด | `openclaw cron list` | แก้ cron / timezone |

---

## 15. Teaching Flow: 3 ชั่วโมง

| เวลา | กิจกรรม | เป้าหมาย |
|---|---|---|
| 0:00–0:15 | เปิดบทเรียนและภาพรวม AI Agent | สร้าง mental model |
| 0:15–0:35 | OpenClaw architecture | เข้าใจ gateway/provider/tool/channel |
| 0:35–1:00 | OpenAI GPT-5.x + Responses API + Tools | เข้าใจ API สมัยใหม่ |
| 1:00–1:20 | Demo: model status / provider routing | เห็นคำสั่งจริง |
| 1:20–1:30 | Break | - |
| 1:30–2:00 | Lab: prompt pattern + structured output | ฝึกออกแบบ prompt |
| 2:00–2:25 | Lab: Cron + Telegram scenario | เห็น automation |
| 2:25–2:45 | Security, cost, rate limit, context overflow | เข้าใจ risk governance |
| 2:45–3:00 | Quiz / discussion / wrap-up | ประเมินผล |

---

## 16. Slide Outline สำหรับผู้สอน

1. AI Agent คืออะไร
2. Chatbot vs Agent vs Automation
3. OpenClaw Architecture
4. OpenAI GPT-5.x Model Strategy
5. Responses API, Function Calling, Structured Outputs
6. OpenRouter และแนวคิด Free Model Routing
7. CLI Demo: install/status/model/provider
8. Prompt Pattern ที่ใช้สอนได้จริง
9. Cron Automation Scenario
10. Security & Cost Governance
11. Troubleshooting Playbook
12. Assignment & Rubric

---

## 17. Quiz สำหรับนิสิต

### Multiple Choice

1. OpenClaw ทำหน้าที่หลักคล้ายข้อใดมากที่สุด?
   - A. Database Server
   - B. AI Agent Gateway / Orchestration Layer
   - C. Spreadsheet Tool
   - D. Static Website Generator

2. ข้อใดเป็น model ref ที่สะท้อน provider/model pattern?
   - A. `localhost:3000`
   - B. `openai/gpt-5.6-sol`
   - C. `npm install`
   - D. `index.html`

3. เหตุใดจึงไม่ควรใช้ model ราคาแพงกับ cron ทุกงาน?
   - A. เพราะ cron ใช้ไม่ได้กับ AI
   - B. เพราะอาจเกิดค่าใช้จ่ายสะสมโดยไม่จำเป็น
   - C. เพราะ model ใหญ่ตอบไม่ได้
   - D. เพราะ OpenClaw ไม่รองรับ cron

### Short Answer

1. อธิบายความแตกต่างระหว่าง `openai/<model>` กับ `openrouter/<provider>/<model>`
2. ยกตัวอย่างความเสี่ยง 3 ข้อของ tool-enabled agent
3. ออกแบบ prompt สำหรับ agent ที่ช่วยสรุปบทเรียน 1 หน้า พร้อมเงื่อนไข output

---

## 18. Assignment

ให้นิสิตออกแบบ AI Agent Scenario 1 งาน โดยต้องมี:

1. Use Case
2. User Persona
3. Model Strategy
4. Prompt Pattern
5. Tool ที่ต้องใช้
6. Security Checklist
7. Cost Control Plan
8. Troubleshooting Plan

### Rubric

| เกณฑ์ | คะแนน |
|---|---:|
| อธิบาย use case ชัดเจน | 20 |
| เลือก model/provider เหมาะสม | 20 |
| prompt มี role/task/context/output/constraint | 20 |
| ระบุความเสี่ยงด้าน security/cost | 20 |
| มี troubleshooting ที่ใช้งานได้จริง | 20 |
| รวม | 100 |

---

## 19. Instructor Notes

- สำหรับห้องเรียนจริง ควรเตรียม API key แบบ demo ที่จำกัดวงเงินไว้แล้ว
- ไม่ควรให้นักศึกษาใช้ production account ส่วนตัวใน live demo
- ถ้าจะใช้ OpenRouter free model ให้เตรียม fallback เพราะ free model อาจเต็ม เปลี่ยนชื่อ หรือถูก deprecate ได้
- ให้สอนนักศึกษาว่า “AI Agent ที่ดี” ไม่ใช่ตอบยาวที่สุด แต่ต้องควบคุมแหล่งข้อมูล รูปแบบ คำสั่ง เครื่องมือ และความเสี่ยงได้
- ทุก lab ควรมี rollback หรือ cleanup step

---

## 20. References / External Sources

1. OpenAI. (2026). *Models - OpenAI API*. Retrieved August 1, 2026, from https://developers.openai.com/api/docs/models
2. OpenAI Help Center. (2026). *Function Calling in the OpenAI API*. Retrieved August 1, 2026, from https://help.openai.com/en/articles/8555517
3. OpenClaw. (2026). *Models CLI*. Retrieved August 1, 2026, from https://docs.openclaw.ai/cli/models
4. OpenClaw. (2026). *Model providers*. Retrieved August 1, 2026, from https://docs.openclaw.ai/concepts/model-providers
5. OpenRouter. (2026). *OpenClaw Integration*. Retrieved August 1, 2026, from https://openrouter.ai/docs/cookbook/coding-agents/openclaw-integration
6. OpenAI Agents SDK. (2026). *Tools*. Retrieved August 1, 2026, from https://openai.github.io/openai-agents-python/tools/

---

## 21. Version Log

| Version | Date | Change |
|---|---|---|
| 1.0 | 2026-08-01 | Created trainer-ready lesson from external documentation research |
