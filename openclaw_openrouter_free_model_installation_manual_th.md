# คู่มือการติดตั้ง OpenClaw กับ OpenRouter Free Model

> เวอร์ชันเอกสาร: v1.1  
> วันที่ปรับปรุง: 1 สิงหาคม 2026  
> ผู้จัดทำ: อ.เอก / AorAke  
> เหมาะสำหรับ: ใช้สอน ใช้แชร์ ใช้เป็น Runbook ติดตั้งจริง และใช้เป็นเอกสารประกอบ Workshop  
> กลุ่มเป้าหมาย: วิทยากรด้าน IT, นักศึกษา IT / CS / Software Engineering / Digital Business, ผู้เริ่มต้นสร้าง AI Agent  
> ขอบเขต: macOS เป็นหลัก และสามารถปรับใช้กับ Linux / WSL2 ได้  
> แหล่งข้อมูลที่ใช้ปรับปรุง: OpenClaw Docs, OpenRouter Docs, OpenRouter Free Models Router, OpenRouter Quickstart

---

## สารบัญ

1. [เป้าหมายของคู่มือนี้](#เป้าหมายของคู่มือนี้)
2. [สิ่งที่ปรับปรุงใน v1.1](#สิ่งที่ปรับปรุงใน-v11)
3. [ภาพรวม OpenClaw + OpenRouter](#บทที่-1-ภาพรวม-openclaw--openrouter)
4. [คำศัพท์สำคัญ](#บทที่-2-คำศัพท์สำคัญ)
5. [OpenRouter Free Model คืออะไร](#บทที่-3-openrouter-free-model-คืออะไร)
6. [เตรียมเครื่องก่อนติดตั้ง](#บทที่-4-เตรียมเครื่องก่อนติดตั้ง)
7. [ติดตั้ง OpenClaw](#บทที่-5-ติดตั้ง-openclaw)
8. [Onboarding OpenRouter](#บทที่-6-onboarding-openrouter)
9. [ตั้งค่า Free Model ใน OpenClaw](#บทที่-7-ตั้งค่า-free-model-ใน-openclaw)
10. [ตรวจสอบ Model และ Auth](#บทที่-8-ตรวจสอบ-model-และ-auth)
11. [ตั้งค่า Web Search](#บทที่-9-ตั้งค่า-web-search)
12. [สร้าง Cron Job แบบประหยัด](#บทที่-10-สร้าง-cron-job-แบบประหยัด)
13. [อ่านไฟล์ เขียนไฟล์ และ Workspace](#บทที่-11-อ่านไฟล์-เขียนไฟล์-และ-workspace)
14. [Telegram Recovery](#บทที่-12-telegram-recovery)
15. [Cost Control และ Rate Limit](#บทที่-13-cost-control-และ-rate-limit)
16. [Context Overflow Playbook](#บทที่-14-context-overflow-playbook)
17. [Security & Token Hygiene](#บทที่-15-security--token-hygiene)
18. [Troubleshooting](#บทที่-16-troubleshooting)
19. [Workshop สำหรับผู้สอน](#บทที่-17-workshop-สำหรับผู้สอน)
20. [Assignment / Lab สำหรับนักศึกษา](#บทที่-18-assignment--lab-สำหรับนักศึกษา)
21. [Command Cheat Sheet](#บทที่-19-command-cheat-sheet)
22. [แหล่งข้อมูลภายนอก](#แหล่งข้อมูลภายนอก)
23. [สรุป](#สรุป)

---

## เป้าหมายของคู่มือนี้

คู่มือนี้ออกแบบเพื่อให้ผู้เรียนสามารถติดตั้งและใช้งาน **OpenClaw** ร่วมกับ **OpenRouter Free Model** ได้อย่างเป็นระบบ โดยเน้น 4 แกนหลัก:

1. ติดตั้งและตรวจสอบ OpenClaw ให้พร้อมใช้งาน
2. เชื่อมต่อ OpenRouter ด้วย OAuth หรือ API Key อย่างปลอดภัย
3. ตั้งค่าโมเดลแบบ free-only ให้ลดความเสี่ยงเรื่องค่าใช้จ่าย
4. นำไปใช้สอน Workshop, Lab และงาน Automation เบื้องต้นได้

เมื่อเรียนจบ ผู้เรียนควรทำได้ดังนี้:

- อธิบายสถาปัตยกรรม OpenClaw + OpenRouter ได้
- แยกความแตกต่างระหว่าง OpenRouter model slug กับ OpenClaw model ref ได้
- ติดตั้ง OpenClaw และเปิด Dashboard/Gateway ได้
- เชื่อม OpenRouter ผ่าน OAuth หรือ API Key ได้
- ตั้งค่า Free Model Router หรือ free variant ได้
- ตรวจสอบ model/auth ด้วยคำสั่ง `openclaw models status`, `models list`, `models scan`, และ `models status --probe` ได้
- สร้าง Cron Job แบบประหยัด token ได้
- วางแนวปฏิบัติด้าน security, token hygiene, rate limit และ context overflow ได้

---

## สิ่งที่ปรับปรุงใน v1.1

เวอร์ชันนี้ปรับปรุงจากเอกสารเดิมโดยอ้างอิงแหล่งข้อมูลภายนอกล่าสุด และเพิ่มประเด็นที่เหมาะกับการใช้สอนจริงมากขึ้น ได้แก่:

| ประเด็นใหม่ | เหตุผลที่เพิ่ม |
|---|---|
| OpenRouter OAuth onboarding | OpenClaw รองรับการ onboarding แบบ OAuth/PKCE สำหรับ OpenRouter |
| OpenRouter API-key onboarding | ใช้ได้กับผู้เรียนที่ต้องการควบคุม key เอง |
| แยก Direct API slug กับ OpenClaw model ref | ลดความสับสนระหว่าง `openrouter/free` และ `openrouter/openrouter/free` |
| `openclaw models scan` | ใช้ตรวจ free-model catalog และความสามารถของ model |
| Model ref pattern | OpenClaw ใช้รูปแบบ `provider/model`; กรณี OpenRouter ที่มี `/` ต้องใส่ provider prefix ให้ครบ |
| Free Model Router behavior | `openrouter/free` เป็น router ที่สุ่ม/เลือกโมเดลฟรีและกรองตามความสามารถของคำขอ |
| OpenRouter Quickstart / API compatibility | OpenRouter ใช้ endpoint แบบ OpenAI-compatible ได้ |
| Workshop & Assignment | เพิ่มโครงสอนสำหรับวิทยากรและกิจกรรมให้นักศึกษา |

---

# บทที่ 1: ภาพรวม OpenClaw + OpenRouter

## 1.1 OpenClaw คืออะไร

**OpenClaw** คือระบบ AI Agent ที่ทำหน้าที่เป็น gateway ระหว่างผู้ใช้ ช่องทางสื่อสาร เครื่องมือ และ model provider หลายรูปแบบ

โครงสร้างโดยย่อ:

```text
User / Student / Instructor
        ↓
Channel Layer
- Dashboard
- Telegram
- CLI
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
- Local Model
        ↓
Tool Layer
- Web Search
- Files
- Cron
- Automation
- Local Workspace
        ↓
Output
- Summary
- Report
- Chat Response
- Action Items
- Teaching Demo
```

## 1.2 OpenRouter คืออะไร

**OpenRouter** คือบริการรวม model หลายค่ายไว้หลัง API เดียว โดยใช้ API key ของ OpenRouter หนึ่งตัวในการเรียกใช้งานโมเดลหลาย provider ได้ เช่น OpenAI-compatible model, open-weight model, reasoning model, coding model และ free model บางรายการ

จุดเด่นสำหรับการสอน:

- ใช้ API รูปแบบใกล้เคียง OpenAI Chat Completions
- เหมาะกับการสาธิต model routing
- สามารถสอนเรื่อง model selection, cost, rate limit และ provider fallback ได้ง่าย
- มี free model / free router สำหรับการทดลองหรือ workshop เบื้องต้น

---

# บทที่ 2: คำศัพท์สำคัญ

| คำศัพท์ | ความหมาย | ตัวอย่าง |
|---|---|---|
| Provider | ผู้ให้บริการ model | `openrouter`, `openai`, `anthropic` |
| Model slug | ชื่อโมเดลในระบบ provider | `openrouter/free`, `google/gemini-*` |
| OpenClaw model ref | รูปแบบที่ OpenClaw ใช้ระบุ provider/model | `openrouter/openrouter/free` |
| Auth profile | โปรไฟล์ credential ที่ OpenClaw เก็บไว้ | `openrouter:default` |
| Gateway | ตัวกลางที่รับคำสั่งและเรียก model/tools | OpenClaw Gateway |
| Dashboard | UI สำหรับควบคุม/ตรวจระบบ | `http://127.0.0.1:18789` |
| Cron Job | งานอัตโนมัติตามเวลา | Daily brief เวลา 08:00 |
| Probe | การทดสอบว่า model ใช้งานได้จริง | `openclaw models status --probe` |

## 2.1 จุดที่ผู้เรียนมักสับสน

### Direct OpenRouter API

เมื่อเรียก OpenRouter API โดยตรง จะใช้ model slug เช่น:

```text
openrouter/free
```

### OpenClaw Model Reference

แต่ใน OpenClaw จะมี provider prefix ข้างหน้าอีกชั้นหนึ่ง เพราะ OpenClaw ต้องรู้ว่าจะส่งคำขอไปที่ provider ใด

ดังนั้น free router ของ OpenRouter มักอ้างอิงใน OpenClaw ได้ในรูปแบบ:

```text
openrouter/openrouter/free
```

ถ้าระบบของผู้เรียนรองรับ alias เดิม อาจพบว่าใช้ได้ทั้ง:

```text
openrouter/free
openrouter/openrouter/free
```

แนวทางสอนที่ปลอดภัยคือ:

```bash
openclaw models list --provider openrouter
openclaw models scan
```

แล้วเลือก model ref ที่ปรากฏจริงในเครื่องของผู้เรียนก่อนตั้งค่า default

---

# บทที่ 3: OpenRouter Free Model คืออะไร

## 3.1 Free Models Router

OpenRouter มี router ชื่อ `openrouter/free` ซึ่งออกแบบเพื่อเลือก free model ที่พร้อมใช้งานจาก catalog ของ OpenRouter โดย router จะพิจารณาความสามารถที่คำขอต้องใช้ เช่น image understanding, tool calling หรือ structured outputs

เหมาะสำหรับ:

- ทดลองเรียนรู้ API
- สอนแนวคิด model routing
- demo งานเบา
- สร้าง prototype แบบไม่เน้น production SLA
- งานสรุปสั้น ๆ หรือ classification เบื้องต้น

ไม่เหมาะสำหรับ:

- งาน production ที่ต้องการเสถียรสูง
- งานที่ต้องการคำตอบยาวมาก
- งานที่ต้องใช้ context ใหญ่
- งานที่ต้องการ latency ต่ำแน่นอน
- งานที่มีข้อมูลลับหรือข้อมูลส่วนบุคคล หากยังไม่มีนโยบายกำกับชัดเจน

## 3.2 Free Variant Model

บาง model มี suffix หรือ variant แบบ free เช่น:

```text
<provider>/<model>:free
```

ข้อดี:

- คุม model ได้มากกว่า router
- เหมาะกับ lab ที่ต้องการเปรียบเทียบ behavior ของ model เดียวกัน

ข้อจำกัด:

- อาจมี rate limit ต่ำ
- อาจไม่พร้อมใช้งานบางช่วง
- ความสามารถเรื่อง tools หรือ structured outputs อาจต่างกัน

## 3.3 ข้อควรระวังเรื่อง `openrouter/auto`

`openrouter/auto` เป็น automatic routing ที่สะดวก แต่ไม่ควรใช้เป็นค่า default หากเป้าหมายคือ **free-only** เพราะอาจ route ไปยัง model ที่มีค่าใช้จ่ายได้

สำหรับ workshop ที่ต้องการควบคุมงบประมาณ ให้ใช้แนวทางนี้:

```text
Free-only lab        → openrouter/openrouter/free หรือ free variant ที่ตรวจแล้ว
Paid/production demo → ใช้ model เฉพาะ พร้อมงบประมาณและ limit ชัดเจน
```

---

# บทที่ 4: เตรียมเครื่องก่อนติดตั้ง

## 4.1 ตรวจระบบปฏิบัติการ

macOS:

```bash
sw_vers
```

Linux / WSL2:

```bash
uname -a
lsb_release -a 2>/dev/null || cat /etc/os-release
```

## 4.2 ตรวจ Node.js และ npm

```bash
node --version
npm --version
```

ค่าแนะนำสำหรับห้องเรียน:

```text
Node.js LTS หรือใหม่กว่า
npm พร้อมใช้งาน
Terminal ใช้งานได้
Internet ใช้งานได้
Browser เปิดได้
```

macOS ติดตั้ง Node.js ผ่าน Homebrew:

```bash
brew install node
```

ตรวจซ้ำ:

```bash
node --version
npm --version
```

## 4.3 เตรียม Account และ Key

ผู้เรียนควรมี:

- GitHub account ถ้าจะทำ lab repository
- OpenRouter account
- OpenRouter API key หรือเลือกใช้ OAuth onboarding
- Telegram bot/chat id ถ้าจะสอน channel integration
- Password manager สำหรับเก็บ API key

---

# บทที่ 5: ติดตั้ง OpenClaw

## 5.1 วิธีแนะนำ: Installer Script

```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

หลังติดตั้ง ให้ตรวจสอบ:

```bash
openclaw --version
openclaw doctor
openclaw gateway status
```

ผลที่คาดหวัง:

```text
Gateway: running
Dashboard: http://127.0.0.1:18789/
Connectivity: ok
```

## 5.2 วิธีทางเลือก: npm

ใช้เมื่อเครื่องมี Node.js และ npm พร้อมแล้ว

```bash
npm install -g openclaw@latest
openclaw onboard --install-daemon
```

## 5.3 เปิด Dashboard

```bash
openclaw dashboard
```

หรือเปิดผ่าน browser:

```bash
open http://127.0.0.1:18789
```

Linux:

```bash
xdg-open http://127.0.0.1:18789
```

## 5.4 Restart Gateway

```bash
openclaw gateway restart
```

> หมายเหตุ: ถ้าผู้เรียนใช้คำสั่ง `openclaw restart` แล้วไม่สำเร็จ ให้แก้เป็น `openclaw gateway restart`

---

# บทที่ 6: Onboarding OpenRouter

OpenClaw รองรับการเชื่อม OpenRouter ได้ 2 แนวทางหลัก:

1. OAuth onboarding
2. API-key onboarding

## 6.1 วิธีที่ 1: OAuth Onboarding

เหมาะสำหรับผู้เรียนที่ต้องการ sign in ผ่าน browser โดยไม่ต้อง copy key เอง

```bash
openclaw onboard --auth-choice openrouter-oauth
```

สิ่งที่เกิดขึ้น:

- OpenClaw เปิด browser เพื่อเข้าสู่ระบบ OpenRouter
- ใช้ flow แบบ PKCE
- ระบบแลก authorization code เป็น OpenRouter API key
- OpenClaw เก็บผลลัพธ์ไว้ใน auth profile ของ OpenRouter

กรณี server/headless:

- OpenClaw แสดง sign-in URL
- ผู้เรียนเปิด URL บนเครื่องที่มี browser
- หลัง login ให้นำ redirect URL กลับมาวางใน terminal

## 6.2 วิธีที่ 2: API-key Onboarding

เหมาะสำหรับผู้สอนที่ต้องการควบคุม key เอง หรือใช้ใน lab ที่เตรียม key ไว้แล้ว

ขั้นตอน:

1. เข้า OpenRouter
2. ไปที่ API Keys
3. สร้าง key ใหม่
4. เก็บ key ใน password manager
5. รันคำสั่ง:

```bash
openclaw onboard --auth-choice openrouter-api-key
```

หรือใช้คำสั่ง auth โดยตรง:

```bash
openclaw models auth login --provider openrouter --method api-key
```

## 6.3 ตรวจ Auth Profiles

```bash
openclaw models auth list --provider openrouter
```

ถ้าต้องการ login ใหม่หรือ rotate key:

```bash
openclaw models auth login --provider openrouter --method oauth
openclaw models auth login --provider openrouter --method api-key
```

---

# บทที่ 7: ตั้งค่า Free Model ใน OpenClaw

## 7.1 ตรวจ model catalog ก่อน

ก่อนตั้งค่า model ให้ตรวจ catalog เสมอ:

```bash
openclaw models list --provider openrouter
```

ถ้าต้องการตรวจ free model catalog และความสามารถของ model:

```bash
openclaw models scan
```

คำสั่ง scan มีประโยชน์สำหรับผู้สอน เพราะช่วยให้เห็นว่า free model ใดพร้อมใช้งาน และรองรับ capability ใดบ้าง เช่น tool calling หรือ image support

## 7.2 ตั้ง Free Models Router เป็น default

ให้เริ่มจาก model ref ที่ OpenClaw รู้จักจริงจาก `models list` หรือ `models scan`

รูปแบบที่แนะนำสำหรับ OpenClaw:

```bash
openclaw models set openrouter/openrouter/free
```

ถ้าเครื่องหรือ build ของผู้เรียนยังใช้ alias เดิมและคำสั่งข้างต้นไม่ผ่าน ให้ลองตรวจรายการก่อนแล้วจึงใช้:

```bash
openclaw models set openrouter/free
```

## 7.3 ตั้ง fallback

```bash
openclaw models fallbacks clear
openclaw models fallbacks add openrouter/openrouter/free
```

ถ้า build ใช้ alias เดิม:

```bash
openclaw models fallbacks clear
openclaw models fallbacks add openrouter/free
```

## 7.4 ตั้ง alias สำหรับห้องเรียน

```bash
openclaw models aliases add or-free openrouter/openrouter/free
```

ตรวจ alias:

```bash
openclaw models aliases list
```

ใช้งาน alias:

```bash
openclaw models set or-free
```

## 7.5 Restart และ Probe

```bash
openclaw gateway restart
openclaw models status --probe
```

ผลที่คาดหวัง:

```text
Provider: openrouter
Model: openrouter/openrouter/free หรือ alias ที่ตั้งไว้
Probe: ok
```

---

# บทที่ 8: ตรวจสอบ Model และ Auth

## 8.1 ตรวจ default model

```bash
openclaw models status
```

## 8.2 ตรวจแบบ probe

```bash
openclaw models status --probe
```

## 8.3 ตรวจ provider catalog

```bash
openclaw models list --provider openrouter
```

## 8.4 Refresh catalog

ถ้า catalog เก่า หรือเพิ่งเปลี่ยน provider/model:

```bash
openclaw models refresh
openclaw gateway restart
```

## 8.5 ตรวจ config default model

```bash
openclaw config get agents.defaults.model --json
```

## 8.6 Troubleshooting จุดนี้

| อาการ | สาเหตุที่เป็นไปได้ | วิธีตรวจ |
|---|---|---|
| `missing_credential` | ยังไม่มี key/auth profile | `models auth list --provider openrouter` |
| `unresolved_ref` | model ref ไม่ถูกต้อง | `models list --provider openrouter` |
| `no_model` | provider auth มี แต่หา model ไม่เจอ | `models refresh`, `models scan` |
| probe fail | key, quota, network, model unavailable | ตรวจ OpenRouter dashboard และ logs |

---

# บทที่ 9: ตั้งค่า Web Search

ถ้า agent ต้องค้นข้อมูลล่าสุด ต้องตั้งค่า web provider ก่อน

```bash
openclaw configure --section web
```

หลังตั้งค่า:

```bash
openclaw gateway restart
```

ตรวจด้วย prompt สั้น ๆ:

```text
ค้นข่าวเทคโนโลยี AI ล่าสุด 1 ข่าว พร้อมสรุป 3 บรรทัด และใส่แหล่งที่มา
```

แนวทางเลือก provider:

| Provider | เหมาะกับ |
|---|---|
| DuckDuckGo | Lab เร็ว ไม่เน้น API key |
| Brave | ใช้งานจริงมากขึ้น |
| Gemini Search | ต้องการ grounding/citation |
| SearXNG | self-host/privacy |

---

# บทที่ 10: สร้าง Cron Job แบบประหยัด

## 10.1 หลักการออกแบบ Cron สำหรับ Free Model

```text
จำกัดหัวข้อ
จำกัดจำนวนผลลัพธ์
จำกัดความยาวคำตอบ
หลีกเลี่ยง PDF ยาว
หลีกเลี่ยง tool calls จำนวนมาก
ให้ถามก่อนวิเคราะห์เชิงลึก
ใช้ isolated session
```

## 10.2 ตัวอย่าง Daily Ultra-light Brief

```bash
MSG=$(cat <<'EOF'
ทำ Daily AI Discovery แบบ Ultra-light

เงื่อนไข:
- ค้นเฉพาะรายการสำคัญใน 24 ชั่วโมงล่าสุด
- จำกัดไม่เกิน 3 รายการ
- สรุปเป็นภาษาไทย
- ข่าวละไม่เกิน 4 บรรทัด
- ห้ามอ่าน PDF เต็ม
- ห้ามทำบทวิเคราะห์ยาว
- ท้ายข้อความให้ถามว่า “ต้องการรายละเอียดข่าวใดเพิ่มเติมหรือไม่”

รูปแบบผลลัพธ์:
1) สถานะรวม
2) รายการสำคัญ
3) ผลกระทบต่อผู้เรียน IT แบบสั้น
4) แหล่งอ้างอิง
EOF
)

openclaw cron add \
  --name "daily-ai-ultra-light-brief" \
  --cron "0 8 * * *" \
  --tz "Asia/Bangkok" \
  --session isolated \
  --announce \
  --channel telegram \
  --to "<telegram-chat-id>" \
  --model openrouter/openrouter/free \
  --message "$MSG"
```

## 10.3 ตรวจ Cron

```bash
openclaw cron list
openclaw cron run "<job-id>"
openclaw cron runs --id "<job-id>"
```

## 10.4 ปิด/เปิด Cron

```bash
openclaw cron disable "<job-id>"
openclaw cron enable "<job-id>"
```

## 10.5 เปลี่ยน model ของ Cron

```bash
openclaw cron edit "<job-id>" --model openrouter/openrouter/free
```

---

# บทที่ 11: อ่านไฟล์ เขียนไฟล์ และ Workspace

## 11.1 หลักความปลอดภัยก่อนให้ Agent แก้ไฟล์

```text
อ่านก่อน → ระบุ path ให้ชัด → backup → เขียนเฉพาะไฟล์ที่อนุญาต → ตรวจผลหลังแก้
```

## 11.2 สร้าง workspace สำหรับ lab

```bash
mkdir -p "$HOME/AI-Agent-Lab/input"
mkdir -p "$HOME/AI-Agent-Lab/output"
mkdir -p "$HOME/AI-Agent-Lab/backup"
```

## 11.3 อ่านไฟล์

```bash
cat "$HOME/AI-Agent-Lab/input/sample.txt"
head -80 "$HOME/AI-Agent-Lab/input/sample.txt"
tail -80 "$HOME/AI-Agent-Lab/input/sample.txt"
```

## 11.4 ค้นหาในไฟล์

```bash
grep -n "OpenRouter" "$HOME/AI-Agent-Lab/input/sample.txt"
grep -Rni "model" "$HOME/AI-Agent-Lab/input"
```

## 11.5 เขียนไฟล์ Markdown

```bash
cat <<'EOF' > "$HOME/AI-Agent-Lab/output/summary.md"
# Summary

- Topic: OpenClaw + OpenRouter
- Result: System ready for basic lab
EOF
```

## 11.6 Backup ก่อนแก้ไฟล์

```bash
cp "$HOME/AI-Agent-Lab/output/summary.md" \
   "$HOME/AI-Agent-Lab/backup/summary.backup.$(date +%Y%m%d-%H%M%S).md"
```

---

# บทที่ 12: Telegram Recovery

ถ้าใช้งานผ่าน Telegram แล้ว session ค้าง ให้เริ่ม session ใหม่:

```text
/new
```

ทดสอบสั้น ๆ:

```text
ตรวจสถานะสั้น ๆ ว่าพร้อมใช้งานหรือไม่
```

ถ้ายังไม่ตอบ:

```bash
openclaw gateway status
openclaw gateway restart
openclaw models status --probe
```

---

# บทที่ 13: Cost Control และ Rate Limit

## 13.1 หลักควบคุมต้นทุน

| หลักการ | วิธีปฏิบัติ |
|---|---|
| ใช้ free-only | ใช้ `openrouter/openrouter/free` หรือ free variant ที่ตรวจแล้ว |
| หลีกเลี่ยง auto paid routing | ไม่ใช้ `openrouter/auto` เป็นค่า default ใน lab free-only |
| จำกัด prompt | ไม่ส่งเอกสารยาวทั้งหมดถ้าไม่จำเป็น |
| จำกัด output | ระบุความยาว เช่น ไม่เกิน 500–900 คำ |
| จำกัด tool calls | ไม่ให้ค้นหลายเว็บ/หลายไฟล์พร้อมกัน |
| ตั้ง cron ไม่ถี่ | รายวัน/รายสัปดาห์ มากกว่ารายชั่วโมงใน free lab |

## 13.2 ตัวอย่าง Prompt แบบประหยัด

```text
สรุปข้อความต่อไปนี้เป็นภาษาไทย
- ไม่เกิน 300 คำ
- แยกเป็น 5 bullet
- ถ้าข้อมูลไม่พอ ให้บอกว่า “ข้อมูลไม่เพียงพอ”
- ห้ามค้นเว็บเพิ่มเติม
```

## 13.3 Rate Limit Playbook

ถ้าเจอ rate limit:

```bash
sleep 90
openclaw models status --probe
openclaw cron runs --id "<job-id>"
```

ปรับลด:

```text
ลดจำนวนข่าว
ลดคำตอบ
ลดความถี่ cron
ลดจำนวน tool calls
แยกงานใหญ่เป็นหลายรอบ
```

---

# บทที่ 14: Context Overflow Playbook

Context overflow เกิดเมื่อ:

```text
prompt + chat history + file/tool input + expected output > context window
```

วิธีแก้:

1. เริ่ม session ใหม่ด้วย `/new`
2. ลด prompt
3. ตัดเอกสารเป็นช่วง ๆ
4. จำกัด output
5. สรุปทีละส่วนก่อนรวม
6. หลีกเลี่ยงการค้นหลายเว็บพร้อมกัน
7. ใช้ workflow: Discovery → Extract → Analyze → Record

ตัวอย่าง prompt ที่เหมาะกว่า:

```text
อ่านเฉพาะหัวข้อ 1-3 แล้วสรุปไม่เกิน 300 คำ
ยังไม่ต้องวิเคราะห์เชิงลึก
หลังสรุปให้ถามว่าต้องการอ่านหัวข้อถัดไปหรือไม่
```

---

# บทที่ 15: Security & Token Hygiene

## 15.1 ห้ามเปิดเผยข้อมูลเหล่านี้

```text
OPENROUTER_API_KEY
OPENAI_API_KEY
Gateway token
Telegram bot token
Password
Session token
.env
auth-profiles.json
credential files
```

## 15.2 แนวปฏิบัติสำหรับห้องเรียน

- ห้ามให้นักศึกษาส่ง API key ใน chat/public repo
- ใช้ placeholder เช่น `<OPENROUTER_API_KEY>` เสมอ
- ให้ผู้เรียนสร้าง key ของตนเอง
- ใช้ password manager
- ตั้ง spending limit ใน OpenRouter หากมี
- ใช้ key แยกสำหรับ workshop
- ลบ/rotate key หลังจบ workshop ถ้าจำเป็น

## 15.3 ตรวจหาความเสี่ยงก่อน commit

```bash
grep -Rni "sk-or-" .
grep -Rni "OPENROUTER_API_KEY" .
grep -Rni "BOT_TOKEN" .
```

ถ้าเผลอ commit secret:

1. revoke key ทันที
2. สร้าง key ใหม่
3. ลบ secret จากไฟล์
4. พิจารณาทำ history cleanup หาก repo public

---

# บทที่ 16: Troubleshooting

| อาการ | สาเหตุที่เป็นไปได้ | วิธีแก้ |
|---|---|---|
| `401 Unauthorized` | API key ผิด/หมดอายุ | login ใหม่ / rotate key |
| `402 Payment Required` | credit ไม่พอหรือ route ไป paid model | ใช้ free-only ref / เติม credit / ลด token |
| `404 model not found` | model ref ไม่ตรง catalog | `models list --provider openrouter`, `models scan` |
| `No allowed providers` | provider/model ไม่พร้อมหรือ policy ไม่อนุญาต | เปลี่ยน model / refresh catalog |
| `missing_credential` | ไม่มี auth profile | `models auth login --provider openrouter --method api-key` |
| `unresolved_ref` | รูปแบบ model ref ผิด | ใช้ `openrouter/<provider>/<model>` |
| `rate limit` | เรียกถี่เกินหรือ free tier จำกัด | รอ / ลดความถี่ / ลด output |
| `context overflow` | prompt หรือ input ใหญ่เกิน | ลด context / แยกงาน |
| `web_search disabled` | ยังไม่ตั้ง web provider | `openclaw configure --section web` |
| Telegram ค้าง | session ค้างหรือ gateway มีปัญหา | `/new`, restart gateway |

## 16.1 Debug Commands

```bash
openclaw doctor
openclaw gateway status
openclaw gateway restart
openclaw models status
openclaw models status --probe
openclaw models list --provider openrouter
openclaw models scan
openclaw models auth list --provider openrouter
openclaw cron list
openclaw logs --help
openclaw logs --follow
```

---

# บทที่ 17: Workshop สำหรับผู้สอน

## 17.1 โครงเวลา 3 ชั่วโมง

| เวลา | กิจกรรม | ผลลัพธ์ |
|---|---|---|
| 0:00–0:20 | อธิบายภาพรวม AI Agent, OpenClaw, OpenRouter | ผู้เรียนเข้าใจ architecture |
| 0:20–0:45 | เตรียมเครื่องและติดตั้ง OpenClaw | เปิด gateway/dashboard ได้ |
| 0:45–1:15 | Onboarding OpenRouter | มี auth profile พร้อมใช้งาน |
| 1:15–1:40 | ตั้ง free model และ probe | model ตอบได้ |
| 1:40–2:10 | ทดลอง prompt และ web search | เห็น agent workflow |
| 2:10–2:35 | สร้าง cron job แบบประหยัด | มี automation demo |
| 2:35–2:50 | Security / token hygiene | เข้าใจความเสี่ยง |
| 2:50–3:00 | Q&A / assignment briefing | ส่งงานต่อได้ |

## 17.2 Teaching Script แบบย่อ

```text
วันนี้เราไม่ได้เรียนแค่การเรียก LLM แต่เรียนการจัดระบบ AI Agent ให้ทำงานจริงอย่างปลอดภัย
OpenClaw คือ gateway และ runtime orchestration
OpenRouter คือ model provider gateway ที่รวมโมเดลหลายค่ายไว้หลัง API เดียว
หัวใจของ lab คือการเลือก model ให้ถูก ใช้ credential ให้ปลอดภัย และควบคุมต้นทุนให้ได้
```

## 17.3 จุดเน้นสำหรับวิทยากร

- ย้ำว่า free model ไม่เท่ากับ unlimited
- ย้ำว่า API key คือความลับ
- ย้ำว่า `openrouter/auto` ไม่ใช่ free-only guarantee
- สอนให้ผู้เรียนตรวจ catalog ก่อนตั้งค่า model
- ให้ผู้เรียนทำ lab ด้วย prompt สั้นก่อน
- ห้ามใช้ข้อมูลจริงที่เป็นความลับในห้องเรียน

---

# บทที่ 18: Assignment / Lab สำหรับนักศึกษา

## Lab 1: ตรวจระบบและติดตั้ง

ภารกิจ:

1. ตรวจ Node.js/npm
2. ติดตั้ง OpenClaw
3. เปิด Dashboard
4. ส่ง screenshot หรือ command output ที่ไม่มี secret

ผลลัพธ์ที่ต้องส่ง:

```text
openclaw --version
openclaw gateway status
```

## Lab 2: เชื่อม OpenRouter และตั้ง Free Model

ภารกิจ:

1. เชื่อม OpenRouter ด้วย OAuth หรือ API key
2. ตรวจ auth profile
3. list model provider
4. ตั้ง free model
5. probe

ผลลัพธ์ที่ต้องส่ง:

```text
openclaw models auth list --provider openrouter
openclaw models list --provider openrouter
openclaw models status --probe
```

> ห้ามส่ง API key ในรายงาน

## Lab 3: Prompt Design แบบประหยัด

ให้ผู้เรียนออกแบบ prompt สำหรับงานใดงานหนึ่ง:

- สรุปข่าว IT
- สรุป lecture note
- จัดหมวด ticket support
- สร้าง checklist ตรวจระบบ

เงื่อนไข:

```text
ไม่เกิน 500 คำ
ต้องมีข้อจำกัด output
ต้องระบุว่าห้ามค้นเว็บถ้าไม่จำเป็น
ต้องมี fallback phrase เช่น “ข้อมูลไม่เพียงพอ”
```

## Lab 4: Cron Automation

ให้สร้าง cron job ที่ทำงานวันละครั้ง และตอบไม่เกิน 700 คำ

ตัวอย่างหัวข้อ:

- Daily AI News Brief
- Daily GitHub Learning Reminder
- Weekly Study Summary

## Rubric

| เกณฑ์ | คะแนน |
|---|---:|
| ติดตั้งและตรวจระบบได้ | 20 |
| ตั้ง OpenRouter และ model ได้ถูกต้อง | 25 |
| ควบคุม security และไม่เปิดเผย secret | 20 |
| Prompt มีข้อจำกัดชัดเจน | 15 |
| Cron design ประหยัดและตรวจสอบได้ | 10 |
| รายงานอ่านง่าย มีหลักฐาน command output | 10 |

---

# บทที่ 19: Command Cheat Sheet

## System

```bash
openclaw --version
openclaw doctor
openclaw gateway status
openclaw gateway restart
openclaw dashboard
```

## OpenRouter Auth

```bash
openclaw onboard --auth-choice openrouter-oauth
openclaw onboard --auth-choice openrouter-api-key
openclaw models auth login --provider openrouter --method oauth
openclaw models auth login --provider openrouter --method api-key
openclaw models auth list --provider openrouter
```

## Models

```bash
openclaw models status
openclaw models status --probe
openclaw models list --provider openrouter
openclaw models refresh
openclaw models scan
openclaw models set openrouter/openrouter/free
openclaw models aliases add or-free openrouter/openrouter/free
openclaw models aliases list
openclaw models fallbacks clear
openclaw models fallbacks add openrouter/openrouter/free
```

## Web Search

```bash
openclaw configure --section web
openclaw gateway restart
```

## Cron

```bash
openclaw cron list
openclaw cron run "<job-id>"
openclaw cron runs --id "<job-id>"
openclaw cron disable "<job-id>"
openclaw cron enable "<job-id>"
openclaw cron edit "<job-id>" --model openrouter/openrouter/free
```

## Logs

```bash
openclaw logs --help
openclaw logs --follow
```

---

## แหล่งข้อมูลภายนอก

เอกสารนี้ปรับปรุงโดยอ้างอิงแหล่งข้อมูลหลักต่อไปนี้:

1. OpenClaw Models CLI  
   https://docs.openclaw.ai/cli/models

2. OpenClaw Model Providers  
   https://docs.openclaw.ai/concepts/model-providers

3. OpenClaw OpenRouter Provider  
   https://docs.openclaw.ai/openrouter

4. OpenRouter Quickstart  
   https://openrouter.ai/docs/quickstart

5. OpenRouter Free Models Router  
   https://openrouter.ai/openrouter/free

6. OpenRouter Models Catalog  
   https://openrouter.ai/models

---

## สรุป

OpenClaw + OpenRouter Free Model เหมาะสำหรับการเรียนรู้และสาธิตการสร้าง AI Agent ในระดับ practical workshop เพราะช่วยให้ผู้เรียนเห็นภาพครบตั้งแต่การติดตั้ง การตั้งค่า provider/model การเชื่อม auth การทดสอบ model การสร้าง cron automation และการจัดการความเสี่ยงด้าน token/cost

ค่าที่แนะนำสำหรับ lab แบบ free-only:

```text
Provider            = openrouter
Direct API slug      = openrouter/free
OpenClaw model ref   = openrouter/openrouter/free
Alias แนะนำ          = or-free
Cron frequency       = รายวันหรือรายสัปดาห์
Output limit         = 500–900 คำ
Security rule        = ห้ามเปิดเผย API key ทุกกรณี
```

แนวคิดสำคัญที่สุด:

```text
ตรวจ catalog ก่อนตั้ง model
ใช้ free-only ref เมื่อต้องการควบคุมค่าใช้จ่าย
จำกัด prompt/output/tool calls
backup ก่อนให้ agent เขียนไฟล์
ไม่เปิดเผย secret
ใช้ /new หรือ restart gateway เมื่อติด session
```
