# คู่มือการติดตั้ง OpenClaw กับ OpenRouter Free Model

> เวอร์ชันเอกสาร: v1.0  
> ผู้จัดทำ: อ.เอก / AorAke  
> เหมาะสำหรับ: ใช้สอน ใช้แชร์ ใช้เป็น Runbook ติดตั้งจริง  
> ขอบเขต: macOS เป็นหลัก และสามารถปรับใช้กับ Linux / WSL2 ได้

---

## เป้าหมายของคู่มือนี้

คู่มือนี้ออกแบบเพื่อให้ผู้เรียนสามารถติดตั้งและใช้งาน OpenClaw ร่วมกับ OpenRouter โดยเน้นการใช้ **Free Model / Free Router** ให้ปลอดภัย ลดค่าใช้จ่าย และหลีกเลี่ยงปัญหา credit หมดจากการใช้ `openrouter/auto` หรือ model แบบ paid โดยไม่ตั้งใจ

เมื่อเรียนจบ ผู้เรียนควรทำได้ดังนี้

1. ติดตั้ง OpenClaw ได้
2. เปิด Gateway และ Dashboard ได้
3. สมัคร/สร้าง OpenRouter API Key ได้อย่างปลอดภัย
4. ตั้งค่า OpenClaw ให้ใช้ OpenRouter ได้
5. ตั้งค่าให้ใช้ OpenRouter Free Model ได้
6. ตรวจสอบ model ด้วย `models status --probe` ได้
7. สร้าง cron job แบบประหยัด token ได้
8. แก้ปัญหาเบื้องต้น เช่น billing error, context overflow, missing auth, web_search disabled ได้

---

# บทที่ 1: เข้าใจภาพรวม OpenClaw + OpenRouter

## 1.1 OpenClaw คืออะไร

OpenClaw คือระบบ AI Agent ที่รันบนเครื่องของผู้ใช้ มี Gateway, Dashboard, Model Provider, Channel เช่น Telegram และระบบ Cron สำหรับสั่งงานอัตโนมัติ

โครงสร้างโดยย่อ:

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

## 1.2 OpenRouter คืออะไร

OpenRouter คือ service ที่รวม model หลายค่ายไว้หลัง API เดียว เช่น Qwen, Llama, DeepSeek, Gemini, GPT OSS และอื่น ๆ โดยใช้ API key ของ OpenRouter เพียงตัวเดียว

## 1.3 Free Model คืออะไร

OpenRouter มี 2 แนวทางหลักสำหรับ model ฟรี

| ประเภท | ความหมาย | เหมาะกับ |
|---|---|---|
| `openrouter/free` | ให้ OpenRouter เลือก free model ที่พร้อมใช้ให้เอง | ทดลอง / งานเบา / งานสอน |
| `model-id:free` | เลือก free variant ของ model เฉพาะ | ต้องการคุม model มากขึ้น |

ข้อจำกัดสำคัญ:

- Free model อาจมี rate limit ต่ำกว่า paid model
- บางช่วงอาจช้าหรือไม่ว่าง
- ไม่เหมาะกับงาน production ที่ต้องการความเสถียรสูง
- ถ้าใช้ `openrouter/auto` อาจ route ไป model ที่คิดเงินได้ จึงไม่ควรใช้เมื่อต้องการ free-only

---

# บทที่ 2: เตรียมเครื่องก่อนติดตั้ง

## 2.1 ตรวจ macOS และ Node.js

เปิด Terminal แล้วรัน:

```bash
sw_vers
node --version
npm --version
```

ค่าแนะนำ:

```text
Node.js 24 recommended
หรืออย่างน้อย Node.js 22.14+
```

ถ้ายังไม่มี Node.js สามารถติดตั้งผ่าน Homebrew:

```bash
brew install node
```

ตรวจซ้ำ:

```bash
node --version
npm --version
```

---

# บทที่ 3: ติดตั้ง OpenClaw

## 3.1 วิธีแนะนำ: Installer Script

```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

ตัวติดตั้งจะช่วยตรวจระบบ ติดตั้ง Node ถ้าจำเป็น ติดตั้ง OpenClaw และเริ่ม onboarding

## 3.2 วิธีทางเลือก: npm

ใช้เมื่อผู้เรียนมี Node.js พร้อมแล้ว

```bash
npm install -g openclaw@latest
openclaw onboard --install-daemon
```

## 3.3 ตรวจว่าติดตั้งสำเร็จ

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

# บทที่ 4: เปิด Dashboard และตรวจ Gateway

## 4.1 เปิด Dashboard

```bash
openclaw dashboard
```

หรือเปิดตรงด้วย browser:

```bash
open http://127.0.0.1:18789
```

## 4.2 Restart Gateway

คำสั่งที่ถูกต้องคือ:

```bash
openclaw gateway restart
```

ไม่ใช่:

```bash
openclaw restart
```

เพราะ OpenClaw ไม่มีคำสั่ง `openclaw restart` ตรง ๆ

## 4.3 ชุดตรวจสุขภาพระบบ

```bash
openclaw gateway status
openclaw models status
openclaw models status --probe
openclaw cron list
```

---

# บทที่ 5: สร้าง OpenRouter API Key อย่างปลอดภัย

## 5.1 วิธีสร้าง API Key

1. เข้า OpenRouter
2. Login
3. ไปที่เมนู **API Keys**
4. กด **Create Key**
5. ตั้งชื่อ key เช่น `OpenClaw-Teaching-Free-Model`
6. Copy key เก็บไว้ใน password manager หรือที่ปลอดภัย

## 5.2 รูปแบบ key ที่ถูกต้อง

OpenRouter key มักขึ้นต้นประมาณนี้:

```text
sk-or-v1-...
```

ไม่ใช่:

```text
hf_...        # Hugging Face
sk-proj-...   # OpenAI
```

## 5.3 กฎความปลอดภัย

ห้ามส่งข้อมูลเหล่านี้ในแชตหรือเอกสารสาธารณะ:

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

# บทที่ 6: เชื่อม OpenClaw กับ OpenRouter

## 6.1 Login OpenRouter ใน OpenClaw

```bash
openclaw models auth login --provider openrouter
```

ถ้าคำสั่งนี้ไม่รองรับ ให้ใช้ onboarding:

```bash
openclaw onboard --auth-choice openrouter-api-key
```

วาง API key ใน Terminal เท่านั้น ไม่ส่งในแชต

## 6.2 ตรวจสถานะ auth

```bash
openclaw models status
openclaw models status --probe
```

ถ้า auth ผ่าน ควรเห็น provider `openrouter` มีสถานะใช้งานได้

---

# บทที่ 7: ตั้งค่า OpenRouter Free Model

## 7.1 หลักสำคัญ

ถ้าต้องการใช้ **free-only** ห้ามใช้ `openrouter/auto` เป็นค่าแรก เพราะ auto อาจเลือก paid model ได้

ให้ใช้:

```text
openrouter/free
```

หรือ free variant:

```text
<provider>/<model>:free
```

## 7.2 ตรวจ model ที่ OpenClaw เห็น

```bash
openclaw models list --provider openrouter
```

ถ้ารองรับ `--plain`:

```bash
openclaw models list --provider openrouter --plain | grep -i free
```

ถ้า `openrouter/free` ไม่พบ ให้ copy model reference ที่ OpenClaw แสดงจริงมาใช้

## 7.3 ตั้ง default เป็น OpenRouter Free Router

ลองคำสั่งนี้ก่อน:

```bash
openclaw models set openrouter/free
```

ถ้า CLI แจ้ง model not found ให้ลองตรวจ list แล้วใช้ exact model id ที่แสดง เช่นบาง build อาจแสดงเป็น:

```bash
openclaw models set openrouter/openrouter/free
```

## 7.4 ตั้ง fallback ให้เป็น free เช่นกัน

```bash
openclaw models fallbacks clear
openclaw models fallbacks add openrouter/free
```

ถ้า build ของพี่เอกใช้รูปแบบ `openrouter/openrouter/free` ให้ใช้ exact ref เดียวกัน:

```bash
openclaw models fallbacks clear
openclaw models fallbacks add openrouter/openrouter/free
```

## 7.5 เพิ่ม alias เพื่อสอนง่าย

```bash
openclaw models aliases add or-free openrouter/free
```

ถ้าใช้ exact ref อีกแบบ:

```bash
openclaw models aliases add or-free openrouter/openrouter/free
```

## 7.6 Restart และ Probe

```bash
openclaw gateway restart
openclaw models status --probe
```

ผลที่ต้องการ:

```text
Default: openrouter/free
หรือ Default: openrouter/openrouter/free
Probe: ok
```

---

# บทที่ 8: การเลือก free model แบบเฉพาะเจาะจง

## 8.1 ใช้ Free Router ก่อน

เหมาะกับผู้เริ่มต้น:

```bash
openclaw models set openrouter/free
```

ข้อดี:

- ไม่ต้องเลือก model เอง
- ลดโอกาสเลือก model ที่ไม่พร้อมใช้
- เหมาะกับ demo / การสอน / งานเบา

ข้อเสีย:

- ไม่รู้ล่วงหน้าว่าจะได้ model ไหน
- ผลลัพธ์อาจไม่สม่ำเสมอ

## 8.2 ใช้ `:free` variant

เหมาะกับผู้สอนที่ต้องการควบคุม model มากขึ้น

ตัวอย่างแนวคิด:

```text
meta-llama/llama-3.2-3b-instruct:free
qwen/...:free
deepseek/...:free
```

ใน OpenClaw ให้ใช้รูปแบบ OpenRouter model ref ที่ CLI เห็นจริงจาก:

```bash
openclaw models list --provider openrouter
```

แล้วค่อยตั้งค่า:

```bash
openclaw models set <model-ref-ที่-copy-มาจาก-list>
```

---

# บทที่ 9: ทดสอบการใช้งานจริง

## 9.1 ทดสอบผ่าน CLI

```bash
openclaw models status --probe
```

## 9.2 ทดสอบผ่าน Dashboard

```bash
openclaw dashboard
```

แล้วส่งข้อความสั้น ๆ เช่น:

```text
สวัสดี ช่วยตอบสั้น ๆ ว่าระบบพร้อมใช้งานหรือไม่
```

## 9.3 ทดสอบผ่าน Telegram

ส่งใน Telegram:

```text
/new
```

ตามด้วย:

```text
ตรวจสถานะสั้น ๆ
```

ถ้าเคยเจอ context overflow ให้เริ่ม session ใหม่ด้วย `/new` ก่อนเสมอ

---

# บทที่ 10: สร้าง Cron Job แบบประหยัดสำหรับ Free Model

## 10.1 หลักการ Cron สำหรับ Free Model

Free model ไม่เหมาะกับ prompt ยาว งานค้นหลายแหล่ง หรือ output ยาวมาก

กฎที่ควรใช้:

```text
จำกัดผลลัพธ์ไม่เกิน 3 รายการ
ไม่อ่าน PDF เต็ม
ไม่ทำวิเคราะห์เต็ม
ตอบไม่เกิน 600–900 คำ
ถามก่อนทำงานหนักต่อ
```

## 10.2 ตัวอย่าง Cron: Daily Ultra-light Brief

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

ถ้า build ต้องใช้ `openrouter/openrouter/free` ให้เปลี่ยน `--model` เป็น exact ref นั้น

## 10.3 ตรวจ Cron

```bash
openclaw cron list
```

## 10.4 Run test

```bash
openclaw cron run "<job-id>"
openclaw cron runs --id "<job-id>"
```

---

# บทที่ 11: Web Search Provider

## 11.1 ทำไมต้องตั้งค่า web_search

ถ้า prompt ให้ agent “ค้นเว็บ” แต่ยังไม่ได้ตั้ง web provider จะเกิด error ลักษณะ:

```text
web_search is disabled or no provider is available
```

## 11.2 ตั้งค่า web_search แบบไม่ใช้ API key

```bash
openclaw configure --section web
```

เลือก:

```text
duckduckgo
```

แล้ว restart:

```bash
openclaw gateway restart
```

## 11.3 ทางเลือกที่เสถียรกว่า

ถ้าใช้งานจริงระยะยาว ให้พิจารณา Brave Search หรือ provider แบบ API key แต่ต้องระวังค่าใช้จ่ายและไม่เผยแพร่ key

---

# บทที่ 12: ควบคุมค่าใช้จ่ายและป้องกัน credit หมด

## 12.1 หลีกเลี่ยง `openrouter/auto` ถ้าต้องการ free-only

`openrouter/auto` สะดวก แต่สามารถ route ไป model ที่คิดเงินได้ ถ้าบัญชีไม่มี credit เพียงพอจะเจอ error:

```text
402 This request requires more credits
```

## 12.2 ใช้ Free Router แทน

```bash
openclaw models set openrouter/free
openclaw models fallbacks clear
openclaw models fallbacks add openrouter/free
```

## 12.3 ลด prompt และ output

แนวทางลดค่าใช้จ่าย:

```text
ลดจำนวนแหล่งค้นหา
ลดจำนวนรายการผลลัพธ์
ห้ามอ่านเอกสารยาวทั้งฉบับ
ห้ามให้ output ยาว
แยกงาน Discovery กับ Analysis
```

## 12.4 แยกโหมดงาน

| Mode | ใช้ทำอะไร | Model |
|---|---|---|
| Discovery | ค้นเบื้องต้น / สรุปสั้น | openrouter/free |
| Analysis | วิเคราะห์ละเอียด | paid model หรือ model context ใหญ่ |
| Record | บันทึก Excel หลังอนุมัติ | prompt สั้น |

---

# บทที่ 13: การอ่านไฟล์ เขียนไฟล์ และสร้างโฟลเดอร์ใน OpenClaw

บทนี้ออกแบบสำหรับผู้เรียนที่ต้องการให้ OpenClaw ทำงานกับไฟล์จริง เช่น อ่านเอกสารใน workspace, สร้างโฟลเดอร์, เขียนไฟล์ `.md` / `.txt` / `.csv`, และเตรียมโครงสร้างงานสำหรับ AI Agent อย่างปลอดภัย

## 13.1 หลักสำคัญก่อนให้ Agent จัดการไฟล์

ก่อนอนุญาตให้ OpenClaw อ่านหรือเขียนไฟล์ ควรยึดหลักนี้เสมอ:

```text
อ่านก่อน → ตรวจ path → backup ถ้าจะแก้ไฟล์ → เขียนแบบจำกัดขอบเขต → ตรวจผลทันที
```

กฎความปลอดภัย:

```text
- ห้ามให้ Agent ลบไฟล์โดยไม่ตรวจ path
- ห้ามใช้ rm -rf กับ path ที่ไม่แน่ใจ
- ห้ามเขียนทับ config หรือไฟล์สำคัญโดยไม่ backup
- ถ้าเป็น Excel หรือฐานข้อมูล ให้ append-only เป็นหลัก
- ถ้ามี token/API key/password อยู่ในไฟล์ ห้ามให้แสดงในแชต
```

## 13.2 โครงสร้าง Workspace ที่ควรรู้

OpenClaw มักใช้ workspace หลักที่:

```text
~/.openclaw/workspace
```

เปิด workspace:

```bash
open "$HOME/.openclaw/workspace"
```

แสดงรายการไฟล์:

```bash
ls -la "$HOME/.openclaw/workspace"
```

ถ้าต้องการให้ OpenClaw ทำงานกับโฟลเดอร์ความรู้กฎหมายของผู้ใช้ สามารถใช้โครงสร้างตัวอย่าง:

```text
~/Legal-Knowledge/
├── 01_Labour_Law/
├── 02_Civil_Commercial_Code/
├── 03_Social_Security/
├── 04_TLS8001_ISO19011/
├── 09_Case_Law/
└── output/
```

## 13.3 การสร้างโฟลเดอร์

ใช้คำสั่ง `mkdir -p` เพื่อสร้างโฟลเดอร์ โดยไม่ error ถ้าโฟลเดอร์มีอยู่แล้ว

ตัวอย่างสร้างโฟลเดอร์ความรู้กฎหมายแรงงาน:

```bash
mkdir -p "$HOME/Legal-Knowledge/01_Labour_Law"
```

สร้างโฟลเดอร์ output สำหรับทะเบียนกฎหมายแรงงาน:

```bash
mkdir -p "$HOME/Legal-Knowledge/output/labour_law_register"
```

สร้างโฟลเดอร์ output สำหรับคำพิพากษาแรงงาน:

```bash
mkdir -p "$HOME/Legal-Knowledge/output/case_law_log"
```

ตรวจว่าโฟลเดอร์ถูกสร้างแล้ว:

```bash
ls -la "$HOME/Legal-Knowledge/output"
```

## 13.4 การอ่านไฟล์แบบปลอดภัย

### อ่านไฟล์ text ทั้งไฟล์

```bash
cat "$HOME/Legal-Knowledge/INDEX.md"
```

### อ่านเฉพาะส่วนต้นของไฟล์

เหมาะกับไฟล์ยาว:

```bash
head -80 "$HOME/Legal-Knowledge/INDEX.md"
```

### อ่านเฉพาะท้ายไฟล์

```bash
tail -80 "$HOME/Legal-Knowledge/INDEX.md"
```

### ค้นหาคำในไฟล์

```bash
grep -n "ค่าชดเชย" "$HOME/Legal-Knowledge/01_Labour_Law/notes.md"
```

### ค้นหาคำในหลายไฟล์

```bash
grep -Rni "เลิกจ้าง" "$HOME/Legal-Knowledge/01_Labour_Law"
```

## 13.5 การอ่านไฟล์ DOCX บน macOS

ถ้าเป็น `.docx` สามารถใช้ `textutil` เพื่อแปลงเป็น text ชั่วคราวและอ่านใน Terminal

```bash
textutil -convert txt -stdout "$HOME/Legal-Knowledge/01_Labour_Law/example.docx" | head -80
```

เหมาะสำหรับให้ Agent ตรวจสาระเบื้องต้นก่อนสรุปหรือจัดหมวดหมู่

## 13.6 การค้นหาไฟล์ในโฟลเดอร์

ค้นหาไฟล์ทั้งหมดใน Legal-Knowledge:

```bash
find "$HOME/Legal-Knowledge" -maxdepth 3 -type f -print
```

ค้นหาเฉพาะไฟล์ `.md`:

```bash
find "$HOME/Legal-Knowledge" -maxdepth 3 -type f -name "*.md" -print
```

ค้นหาเฉพาะไฟล์ Excel:

```bash
find "$HOME/Legal-Knowledge" -maxdepth 5 -type f -name "*.xlsx" -print
```

## 13.7 การเขียนไฟล์ใหม่แบบปลอดภัย

### สร้างไฟล์ Markdown ใหม่

```bash
cat <<'EOF' > "$HOME/Legal-Knowledge/INDEX.md"
# Legal-Knowledge Index

- 01_Labour_Law: กฎหมายแรงงาน
- 02_Civil_Commercial_Code: ป.พ.พ.
- 09_Case_Law: คำพิพากษา
EOF
```

ข้อควรระวัง:

```text
เครื่องหมาย > จะเขียนทับไฟล์เดิม
ถ้าไฟล์มีอยู่แล้วและสำคัญ ต้อง backup ก่อน
```

### เขียนเพิ่มท้ายไฟล์ ไม่เขียนทับ

ใช้ `>>` เพื่อ append:

```bash
cat <<'EOF' >> "$HOME/Legal-Knowledge/INDEX.md"

## Updated Notes
- เพิ่มรายการใหม่วันนี้
EOF
```

## 13.8 การ backup ก่อนเขียนทับไฟล์

ก่อนเขียนทับไฟล์สำคัญ ให้ backup:

```bash
cp "$HOME/Legal-Knowledge/INDEX.md" \
   "$HOME/Legal-Knowledge/INDEX.backup.$(date +%Y%m%d-%H%M%S).md"
```

จากนั้นค่อยเขียนไฟล์ใหม่

## 13.9 การสร้างไฟล์ CSV สำหรับใช้แทน Excel ชั่วคราว

ถ้า Agent ยังเขียน `.xlsx` ไม่ได้ ให้สร้าง CSV ก่อน แล้วค่อยเปิดด้วย Excel

```bash
cat <<'EOF' > "$HOME/Legal-Knowledge/output/labour_law_register/labour_law_register.csv"
Run_Month,Legal_Title,Legal_Type,Source_Agency,Published_Date,Effective_Date,Key_Summary,Source_URL
2026-05,No significant update,Monthly Log,System,,,,
EOF
```

เปิดไฟล์ CSV:

```bash
open "$HOME/Legal-Knowledge/output/labour_law_register/labour_law_register.csv"
```

## 13.10 การใช้ Python เขียน Excel แบบง่าย

ถ้าต้องเขียน Excel จริง แนะนำใช้ `openpyxl`

ตรวจว่ามี package หรือไม่:

```bash
python3 -m pip show openpyxl
```

ติดตั้งถ้ายังไม่มี:

```bash
python3 -m pip install --user openpyxl
```

ตัวอย่างสร้าง workbook เบื้องต้น:

```bash
python3 <<'PY'
from openpyxl import Workbook
from pathlib import Path

path = Path.home() / "Legal-Knowledge/output/labour_law_register/Labour_Law_Register.xlsx"
path.parent.mkdir(parents=True, exist_ok=True)

wb = Workbook()
ws = wb.active
ws.title = "Labour_Law_Register"
ws.append([
    "Run_Month", "Legal_Title", "Legal_Type", "Source_Agency",
    "Published_Date", "Effective_Date", "Key_Summary", "Source_URL",
    "Duplicate_Key", "Recorded_At"
])

log = wb.create_sheet("Monthly_Run_Log")
log.append([
    "Run_DateTime", "Run_Month", "Total_Items_Found",
    "Total_Items_Added", "Duplicate_Items_Skipped", "Status", "Note"
])

wb.save(path)
print(f"Created: {path}")
PY
```

## 13.11 การ append Excel แบบไม่เขียนทับ

หลักสำคัญ:

```text
- เปิด workbook เดิม
- หาแถวว่างแรก
- append ข้อมูล
- save กลับไฟล์เดิม
- ห้ามสร้าง workbook ใหม่ทับไฟล์เดิมโดยไม่ตั้งใจ
```

ตัวอย่าง append log:

```bash
python3 <<'PY'
from openpyxl import load_workbook
from pathlib import Path
from datetime import datetime

path = Path.home() / "Legal-Knowledge/output/labour_law_register/Labour_Law_Register.xlsx"
wb = load_workbook(path)
ws = wb["Monthly_Run_Log"]

ws.append([
    datetime.now().strftime("%Y-%m-%d %H:%M:%S"),
    datetime.now().strftime("%Y-%m"),
    0,
    0,
    0,
    "OK",
    "No significant update"
])

wb.save(path)
print(f"Updated: {path}")
PY
```

## 13.12 การตรวจ duplicate ก่อนบันทึก

แนวคิด duplicate key:

```text
Duplicate_Key = Legal_Title + Source_Agency + Published_Date + Source_URL
```

ตัวอย่าง Python ตรวจซ้ำแบบง่าย:

```bash
python3 <<'PY'
from openpyxl import load_workbook
from pathlib import Path

path = Path.home() / "Legal-Knowledge/output/labour_law_register/Labour_Law_Register.xlsx"
wb = load_workbook(path)
ws = wb["Labour_Law_Register"]

new_key = "ประกาศตัวอย่าง|กรมสวัสดิการและคุ้มครองแรงงาน|2026-05-03|https://example.com"
existing = set()

# สมมติ Duplicate_Key อยู่ column I = 9
for row in range(2, ws.max_row + 1):
    value = ws.cell(row=row, column=9).value
    if value:
        existing.add(str(value).strip())

if new_key in existing:
    print("DUPLICATE: skip")
else:
    print("NEW: safe to append")
PY
```

## 13.13 การสร้าง symlink ให้ OpenClaw เห็น Legal-Knowledge

ถ้าต้องการให้ workspace ของ OpenClaw ชี้ไปยังโฟลเดอร์ความรู้จริง:

```bash
ln -s "$HOME/Legal-Knowledge" "$HOME/.openclaw/workspace/Legal-Knowledge"
```

ตรวจ symlink:

```bash
ls -la "$HOME/.openclaw/workspace" | grep Legal
```

ถ้ามีอยู่แล้วไม่ต้องสร้างซ้ำ

## 13.14 คำสั่งที่ควรระวังสูง

| คำสั่ง | ความเสี่ยง | คำแนะนำ |
|---|---|---|
| `rm` | ลบไฟล์ | ตรวจ path ก่อน |
| `rm -rf` | ลบทั้งโฟลเดอร์ | หลีกเลี่ยงถ้าไม่จำเป็น |
| `>` | เขียนทับไฟล์ | backup ก่อน |
| `mv` | ย้าย/ทับไฟล์ | ตรวจปลายทางก่อน |
| `chmod` | เปลี่ยนสิทธิ์ | ใช้เฉพาะเมื่อเข้าใจผล |
| `sudo` | สิทธิ์สูง | หลีกเลี่ยงในงาน OpenClaw ปกติ |

## 13.15 แบบฝึกหัดท้ายบท

### แบบฝึกหัดที่ 1: สร้างโฟลเดอร์

ให้ผู้เรียนสร้างโฟลเดอร์:

```bash
mkdir -p "$HOME/Legal-Knowledge/06_Templates"
```

ตรวจผล:

```bash
ls -la "$HOME/Legal-Knowledge"
```

### แบบฝึกหัดที่ 2: เขียนไฟล์ Markdown

```bash
cat <<'EOF' > "$HOME/Legal-Knowledge/06_Templates/README.md"
# Templates

โฟลเดอร์นี้ใช้เก็บแบบฟอร์มและ template สำหรับงาน HR / Compliance / Legal
EOF
```

อ่านไฟล์:

```bash
cat "$HOME/Legal-Knowledge/06_Templates/README.md"
```

### แบบฝึกหัดที่ 3: สร้างไฟล์ CSV

```bash
mkdir -p "$HOME/Legal-Knowledge/output/test"
cat <<'EOF' > "$HOME/Legal-Knowledge/output/test/sample.csv"
Title,Status,Note
OpenClaw,OK,File writing test
EOF
open "$HOME/Legal-Knowledge/output/test/sample.csv"
```

### แบบฝึกหัดที่ 4: Backup ก่อนแก้ไฟล์

```bash
cp "$HOME/Legal-Knowledge/06_Templates/README.md" \
   "$HOME/Legal-Knowledge/06_Templates/README.backup.$(date +%Y%m%d-%H%M%S).md"
```

---

# บทที่ 14: How to Read OpenClaw Status / การอ่านสถานะระบบ

บทนี้เป็นบทสำคัญสำหรับการใช้งานจริง เพราะผู้ใช้ต้องอ่านสถานะระบบให้ออกก่อนแก้ไข ไม่ควรเดาว่าปัญหาอยู่ที่ model, cron, Telegram, web search หรือ credit

## 14.1 คำสั่งตรวจสถานะหลัก

```bash
openclaw gateway status
openclaw models status --probe
openclaw cron list
openclaw cron runs --id "<job-id>"
```

## 14.2 วิธีอ่าน `gateway status`

จุดที่ควรดู:

| จุดตรวจ | ความหมาย |
|---|---|
| `Runtime: running` | Gateway ทำงานอยู่ |
| `Connectivity probe: ok` | CLI ติดต่อ Gateway ได้ |
| `Listening: 127.0.0.1:18789` | Gateway เปิด port แล้ว |
| `Dashboard: http://127.0.0.1:18789/` | Control UI เปิดได้ |
| warning เรื่อง Node/nvm | ยังไม่จำเป็นต้องแก้ทันที ถ้า Gateway ใช้งานได้ |

## 14.3 วิธีอ่าน `models status --probe`

จุดที่ควรดู:

| สถานะ | ความหมาย | วิธีแก้ |
|---|---|---|
| `ok` | model ใช้งานได้ | ใช้ต่อได้ |
| `401 Missing Authentication header` | API key ผิด/หาย/ไม่ถูก provider | login provider ใหม่ |
| `402 requires more credits` | credit ไม่พอ | เติม credit หรือใช้ free model |
| `context overflow` | prompt/tool/output ใหญ่เกิน | ลด prompt/output |
| `No endpoints found` | model route ใช้ไม่ได้ | เปลี่ยน model ref |
| `provider rejected schema` | payload/tool schema ไม่เข้ากับ model | เปลี่ยน model หรือใช้ auto/free router |

## 14.4 วิธีอ่าน `cron list`

ตัวอย่างคอลัมน์สำคัญ:

| คอลัมน์ | ความหมาย |
|---|---|
| `ID` | รหัสงาน ใช้กับ run/edit/enable/disable |
| `Name` | ชื่องาน |
| `Schedule` | รอบเวลาทำงาน |
| `Next` | เวลารันครั้งต่อไป |
| `Last` | เวลารันล่าสุด |
| `Status` | `ok`, `error`, หรือสถานะอื่น |
| `Delivery` | ส่งไปที่ไหน เช่น Telegram |
| `Model` | model ที่ job ใช้งาน |

## 14.5 วิธีอ่าน `cron runs`

```bash
openclaw cron runs --id "<job-id>"
```

ให้ดู:

```text
status: ok/error
summary/error
model/provider
delivered/deliveryStatus
nextRunAtMs
```

ถ้า `delivered: true` แต่ `status: error` หมายความว่า Telegram ส่งข้อความ error ได้ แต่ตัวงานยังล้ม

---

# บทที่ 15: OpenRouter Cost Control / การควบคุมค่าใช้จ่าย

## 15.1 หลักสำคัญ

ถ้าต้องการใช้แบบฟรีหรือสอนผู้เรียน ให้แยกให้ชัดระหว่าง:

| Model Ref | ความหมาย | ความเสี่ยงค่าใช้จ่าย |
|---|---|---|
| `openrouter/free` | free router | ต่ำสุด |
| `<model-id>:free` | free variant เฉพาะ model | ต่ำ |
| `openrouter/auto` | OpenRouter เลือก model ให้ | อาจเสียเงิน |
| paid model โดยตรง | เลือก paid model เอง | เสียเงินตาม usage |

## 15.2 Error 402 คืออะไร

ตัวอย่าง:

```text
402 This request requires more credits, or fewer max_tokens.
```

ความหมาย:

```text
บัญชี OpenRouter มี credit ไม่พอสำหรับ request นี้
```

วิธีแก้:

```text
1) เติม credit
2) ลด prompt/output
3) เปลี่ยนเป็น openrouter/free
4) ปิด cron ที่กิน token ชั่วคราว
```

## 15.3 คำสั่งเปลี่ยนเป็น Free-only Mode

```bash
openclaw models set openrouter/free
openclaw models fallbacks clear
openclaw models fallbacks add openrouter/free
openclaw gateway restart
openclaw models status --probe
```

ถ้า build ใช้ exact ref อีกแบบ ให้ตรวจ:

```bash
openclaw models list --provider openrouter
```

แล้ว copy model ref ที่แสดงจริงมาใช้

## 15.4 วิธีลดค่าใช้จ่ายใน Cron

```text
- จำกัดผลลัพธ์ไม่เกิน 3 รายการ
- ไม่อ่าน PDF เต็ม
- ไม่ให้ output ยาว
- ไม่ทำ analysis เต็มใน cron
- ใช้ Discovery Mode ก่อน
- ถามผู้ใช้ก่อนทำงานหนักต่อ
```

---

# บทที่ 16: Context Overflow Playbook

## 16.1 Context overflow คืออะไร

เกิดเมื่อ:

```text
prompt + tool input + chat history + output ที่ขอ รวมกันเกิน context limit ของ model
```

ตัวอย่าง error:

```text
Context overflow: prompt too large for the model.
This endpoint's maximum context length is 32768 tokens.
```

## 16.2 สาเหตุที่พบบ่อย

| สาเหตุ | ตัวอย่าง |
|---|---|
| prompt ยาวเกิน | ใส่ schema Excel / IRLAC / หลายขั้นตอนในงานเดียว |
| tool input ใหญ่ | ให้ค้นเว็บหลายแหล่งหรืออ่าน PDF เต็ม |
| output limit สูง | model พยายามกันพื้นที่ output 32000 tokens |
| session เก่า | Telegram session มีประวัติเยอะ |

## 16.3 วิธีแก้ทันที

```text
1) ใช้ /new ใน Telegram
2) ลด prompt
3) ลด output ไม่เกิน 700–900 คำ
4) จำกัดผลลัพธ์ไม่เกิน 3 รายการ
5) ไม่อ่าน PDF เต็มใน cron
6) แยก Discovery กับ Analysis
```

## 16.4 Ultra-light Prompt Pattern

```text
ค้นเฉพาะ 7 วันล่าสุด
จำกัดไม่เกิน 3 รายการ
ห้ามอ่าน PDF เต็ม
ห้ามทำบทวิเคราะห์เต็ม
ตอบไม่เกิน 700 คำ
ถ้าไม่พบ ให้ตอบว่า “ไม่พบรายการสำคัญที่เข้าเกณฑ์”
ถามท้ายว่า ต้องการให้วิเคราะห์ต่อหรือไม่
```

---

# บทที่ 17: Cron Job Maintenance / การดูแล Automation

## 17.1 คำสั่งหลัก

```bash
openclaw cron list
openclaw cron run "<job-id>"
openclaw cron runs --id "<job-id>"
openclaw cron edit "<job-id>" --model openrouter/free
openclaw cron disable "<job-id>"
openclaw cron enable "<job-id>"
```

## 17.2 เมื่อไรควร Disable Cron

ควรปิดชั่วคราวเมื่อพบ:

```text
- error ซ้ำหลายครั้ง
- billing error
- context overflow
- web_search disabled
- Telegram target ผิด
- job อยู่ใน backoff
```

คำสั่ง:

```bash
openclaw cron disable "<job-id>"
```

## 17.3 เมื่อไรควร Enable กลับ

เปิดกลับเมื่อ:

```text
- แก้ model แล้ว
- เติม credit แล้ว
- ลด prompt แล้ว
- ตั้ง web_search แล้ว
- ทดสอบ run 1 ครั้งแล้วผ่าน
```

คำสั่ง:

```bash
openclaw cron enable "<job-id>"
```

## 17.4 วิธีแก้ model ให้ cron

```bash
openclaw cron edit "<job-id>" \
  --model openrouter/free
```

หรือถ้ามี credit และต้องการเสถียร:

```bash
openclaw cron edit "<job-id>" \
  --model openrouter/auto
```

## 17.5 Backoff คืออะไร

ถ้า cron error ต่อเนื่อง OpenClaw อาจหน่วงเวลารันครั้งต่อไป เช่น:

```text
consecutiveErrors: 4
backoffMs: 900000
```

วิธีแก้:

```text
ปิด job → แก้ prompt/model/provider → run test → เปิดใหม่
```

---

# บทที่ 18: Telegram Recovery / การกู้คืน Session Telegram

## 18.1 ข้อความ error ที่พบบ่อย

```text
Something went wrong while processing your request.
Please try again, or use /new to start a fresh session.
```

## 18.2 วิธีแก้เบื้องต้น

ใน Telegram ให้ส่ง:

```text
/new
```

จากนั้นทดสอบด้วยข้อความสั้น:

```text
ตรวจสถานะสั้น ๆ
```

## 18.3 เมื่อไรควรใช้ `/new`

```text
- session ค้าง
- context overflow
- model เปลี่ยนใหม่
- agent ตอบไม่จบ
- tool error ต่อเนื่อง
- คุยยาวมากแล้ว
```

## 18.4 Telegram timeout

ถ้า log มี:

```text
telegram/network request-timeout
fetch timeout api.telegram.org
```

มักเป็นปัญหา network ชั่วคราว ไม่ใช่ config เสียเสมอไป

ตรวจด้วย:

```bash
openclaw gateway restart
openclaw cron list
```

---

# บทที่ 19: Web Search Provider Setup

## 19.1 ทำไมต้องตั้ง web_search

ถ้า agent ต้องค้นข่าวหรือกฎหมายล่าสุด แต่ยังไม่ได้ตั้ง web provider จะขึ้น:

```text
web_search is disabled or no provider is available
```

## 19.2 ตั้งค่า Web Search

```bash
openclaw configure --section web
```

เลือก provider ตามความเหมาะสม

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

## 19.3 ทดสอบหลังเปิด web_search

```bash
openclaw cron run "<job-id>"
openclaw cron runs --id "<job-id>"
```

---

# บทที่ 20: Model Strategy for Legal Agent

## 20.1 เลือก model ตามงาน

| งาน | Model Strategy |
|---|---|
| ติดตั้ง/ทดสอบ | `openrouter/free` |
| Cron เบา | `openrouter/free` หรือ free model เฉพาะ |
| Chat ทั่วไป | `openrouter/auto` เมื่อมี credit |
| วิเคราะห์กฎหมาย | model context ใหญ่ / paid model |
| อ่าน PDF ยาว | model context ใหญ่ + แบ่งงานเป็นช่วง |
| Excel append | prompt สั้น / tool-friendly |
| งาน private/offline | Ollama local |

## 20.2 Free-only vs Auto vs Paid

```text
Free-only = ประหยัด แต่ไม่เสถียรเท่า paid
Auto = สะดวก แต่เสี่ยงเสีย credit
Paid = เสถียร/คุณภาพสูงกว่า แต่ต้องควบคุมค่าใช้จ่าย
```

## 20.3 แนวคิด 3 Mode

```text
Discovery Mode: ค้นเบา / free model
Counsel Mode: วิเคราะห์ลึก / context ใหญ่
Record Mode: บันทึกฐานข้อมูล / prompt สั้น / append-only
```

---

# บทที่ 21: AorAke Labour Law Counsel Agent Design

บทนี้ใช้กำหนดบทบาท AorAke AI-Agent ให้เป็นผู้ช่วยทนายความด้านแรงงานเชิงกลยุทธ์

## 21.1 Role

```text
AorAke Labour Law Counsel Agent
```

## 21.2 Mission

```text
ช่วยวิเคราะห์กฎหมายแรงงาน คำพิพากษา ข้อพิพาทแรงงาน HR Compliance และ Audit
โดยยึดความน่าเชื่อถือ ความถูกต้อง และการใช้งานจริง
```

## 21.3 Workflow

```text
ค้นหา → วิเคราะห์ → ประเมินความเสี่ยง → เสนอแนวทาง → ถามอนุมัติ → บันทึกฐานความรู้
```

## 21.4 Guardrails

```text
- ไม่เดากฎหมาย
- ไม่สร้างเลขฎีกาเอง
- ไม่อ้างแหล่งที่ไม่มีจริง
- ใช้แหล่งทางการก่อน
- ถ้า web_search ใช้ไม่ได้ ต้องแจ้งข้อจำกัด
- ถามก่อนบันทึก Excel
- append-only
- ตรวจ duplicate ก่อนบันทึก
```

## 21.5 รูปแบบวิเคราะห์กฎหมายแรงงาน

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

## 21.6 รูปแบบวิเคราะห์คำพิพากษา IRLAC+

```text
I = Issue
R = Rule
L = Legal Source
A = Application
C = Conclusion
+ = HR / Compliance / Audit Impact
```

---

# บทที่ 22: Security Audit / Token Hygiene

## 22.1 ห้ามเปิดเผย secret

ห้ามส่งข้อมูลเหล่านี้ในแชตหรือภาพสาธารณะ:

```text
API key
Telegram bot token
Gateway token
OAuth token
Password
.env
auth-profiles.json
```

## 22.2 ก่อนส่ง log ให้ตรวจอะไร

ค้นหา secret ใน log/workspace:

```bash
grep -RniE "sk-|api[_-]?key|token|secret|password" "$HOME/.openclaw" --exclude-dir=node_modules
```

ถ้าพบ ให้ปิดบังก่อนส่ง

## 22.3 Security warning ที่ควรสนใจ

ถ้าเจอ:

```text
gateway.controlUi.allowInsecureAuth=true
```

ให้ตรวจภายหลังด้วย:

```bash
openclaw security audit
```

## 22.4 ไม่ควรกด Always allow กับคำสั่งเสี่ยง

| คำสั่ง | คำแนะนำ |
|---|---|
| `ls`, `pwd`, `head`, `tail` | Allow once/Always ได้ถ้า path ปลอดภัย |
| `mkdir`, `cp` | Allow once |
| `rm`, `rm -rf`, `sudo`, `chmod`, `chown` | ตรวจ path ก่อน / โดยทั่วไป Allow once เท่านั้น |

---

# บทที่ 23: Node / LaunchAgent / Maintenance

## 23.1 Warning เรื่อง Node จาก nvm

บางครั้ง `gateway status` อาจเตือนว่า:

```text
Gateway service uses Node from a version manager
```

ความหมาย:

```text
OpenClaw service ใช้ Node จาก nvm ซึ่งอาจพังหลังอัปเกรด Node หรือเปลี่ยน path
```

ถ้าระบบยังทำงานได้ ไม่ต้องรีบแก้ทันที

## 23.2 ตรวจสถานะ LaunchAgent

```bash
launchctl list | grep -i openclaw
launchctl print gui/$(id -u)/ai.openclaw.gateway
```

## 23.3 ตรวจ process และ port

```bash
ps aux | grep -i openclaw
lsof -nP -iTCP:18789 -sTCP:LISTEN
```

## 23.4 Repair ภายหลังเมื่อระบบนิ่ง

```bash
openclaw doctor
openclaw doctor --repair
```

ควรใช้เมื่อ:

```text
- ระบบนิ่งแล้ว
- มีเวลาแก้ไข
- backup config แล้ว
```

---

# บทที่ 24: Rollback Plan / แผนย้อนกลับเมื่อ config พัง

## 24.1 Backup config ก่อนแก้

```bash
cp "$HOME/.openclaw/openclaw.json" \
   "$HOME/.openclaw/openclaw.backup.$(date +%Y%m%d-%H%M%S).json"
```

## 24.2 หา backup ล่าสุด

```bash
ls -lt "$HOME/.openclaw" | grep "openclaw.backup"
```

## 24.3 Restore config

```bash
cp "$HOME/.openclaw/openclaw.backup.YYYYMMDD-HHMMSS.json" \
   "$HOME/.openclaw/openclaw.json"

openclaw gateway restart
openclaw models status --probe
```

## 24.4 Rollback model เป็น OpenRouter Free

```bash
openclaw models set openrouter/free
openclaw models fallbacks clear
openclaw models fallbacks add openrouter/free
openclaw gateway restart
openclaw models status --probe
```

## 24.5 ปิด cron ที่ error ก่อน rollback

```bash
openclaw cron disable "<job-id>"
openclaw cron list
```

---

# บทที่ 25: OpenClaw Command Mistake List

| คำสั่งที่ผิด/เสี่ยง | คำสั่งที่ควรใช้ |
|---|---|
| `openclaw restart` | `openclaw gateway restart` |
| `openclaw reset` เพื่อ restart | หลีกเลี่ยง ใช้ gateway restart |
| `openclaw models fallback add` | `openclaw models fallbacks add` |
| ใช้ `openrouter/auto` เมื่ออยากใช้ฟรีเท่านั้น | ใช้ `openrouter/free` |
| run cron error ซ้ำ ๆ | disable → แก้ → run test |
| ส่ง log ที่มี key | ปิดบัง secret ก่อน |

---

# บทที่ 26: Excel Automation Reality Check

## 26.1 ปัญหาที่อาจเจอ

บาง model หรือบาง runtime อาจตอบว่า:

```text
ไม่สามารถจัดการไฟล์ Excel (.xlsx) โดยตรงได้
```

แนวทาง:

```text
1) ให้เขียน CSV ก่อน
2) ใช้ Python openpyxl เขียน Excel
3) แยก Record Mode เป็น prompt สั้น
4) ห้ามให้ cron ทำทั้งค้นเว็บ + วิเคราะห์ + เขียน Excel หนัก ๆ ในรอบเดียว
```

## 26.2 CSV fallback

```bash
cat <<'EOF' > "$HOME/Legal-Knowledge/output/labour_law_register/fallback.csv"
Run_Month,Title,Status,Note
2026-05,No significant update,OK,CSV fallback
EOF
```

## 26.3 Excel append ใช้ Python

ให้ใช้ `openpyxl` และหลัก append-only ตามบทที่ 13

---

# บทที่ 27: Troubleshooting

## 27.1 Error: unknown command `restart`

ผิด:

```bash
openclaw restart
```

ถูก:

```bash
openclaw gateway restart
```

## 27.2 Error: 401 Missing Authentication header

สาเหตุ:

```text
OpenRouter API key ไม่ถูกต้อง หรือใช้ key ผิดค่าย
```

แก้:

```bash
openclaw models auth login --provider openrouter
openclaw gateway restart
openclaw models status --probe
```

## 27.3 Error: 402 Requires more credits

สาเหตุ:

```text
ใช้ paid model หรือ openrouter/auto แล้ว credit ไม่พอ
```

แก้แบบ free-only:

```bash
openclaw models set openrouter/free
openclaw models fallbacks clear
openclaw models fallbacks add openrouter/free
openclaw gateway restart
```

## 27.4 Error: Context overflow

สาเหตุ:

```text
prompt + tool input + output tokens รวมกันเกิน context ของ model
```

แก้:

```text
ลด prompt
ลด output
ไม่อ่าน PDF เต็ม
จำกัดผลลัพธ์ 3 รายการ
ใช้ /new ใน Telegram
ใช้ model context ใหญ่ขึ้นถ้าจำเป็น
```

## 27.5 Error: No endpoints found

สาเหตุ:

```text
model ref ไม่พร้อมใช้งาน หรือ route นั้นไม่มี provider endpoint
```

แก้:

```bash
openclaw models list --provider openrouter
```

แล้ว copy model ref ที่แสดงจริงมาใช้

## 27.6 Error: web_search disabled

แก้:

```bash
openclaw configure --section web
openclaw gateway restart
```

## 27.7 `openclaw logs --tail` ไม่รองรับ

บางเวอร์ชันอาจไม่รองรับ `--tail`

ใช้:

```bash
openclaw logs --help
openclaw logs --follow
```

---

# บทที่ 28: Checklist สำหรับสอนในห้องเรียน / Workshop

## 28.1 Installation Tick List

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

## 28.2 Troubleshooting Tick List

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

## 28.3 Workshop Flow

```bash
node --version
npm --version
openclaw --version
openclaw gateway status
openclaw models status --probe
openclaw cron list
```

---

# บทที่ 29: Best Practices สำหรับใช้งานจริง

## 29.1 อย่าใช้ free model กับงานหนักทันที

งานหนัก เช่น วิเคราะห์คำพิพากษาเต็มฉบับ, อ่าน PDF หลายไฟล์, ทำ IRLAC+ เต็ม ควรแยกเป็นงาน on-demand หรือใช้ model ที่มี context ใหญ่และมี credit เพียงพอ

## 29.2 แยกงาน Discovery กับ Analysis

```text
Discovery = free model / output สั้น
Analysis = paid model หรือ context ใหญ่
Record = บันทึกหลังอนุมัติ
```

## 29.3 ปิด Cron ที่ error บ่อย

```bash
openclaw cron disable "<job-id>"
```

## 29.4 ตรวจ run history

```bash
openclaw cron runs --id "<job-id>"
```

## 29.5 เริ่ม Telegram session ใหม่เมื่อ context เต็ม

```text
/new
```

---

# บทที่ 30: แบบฝึกหัดสำหรับผู้เรียน

## แบบฝึกหัดที่ 1: ตรวจระบบ

ให้ผู้เรียนรัน:

```bash
openclaw gateway status
openclaw models status --probe
openclaw cron list
```

แล้วตอบว่า:

```text
Gateway พร้อมไหม
Model พร้อมไหม
มี Cron กี่งาน
งานใด error
```

## แบบฝึกหัดที่ 2: เปลี่ยน model เป็น free-only

ให้ผู้เรียนตั้งค่า:

```bash
openclaw models set openrouter/free
openclaw models fallbacks clear
openclaw models fallbacks add openrouter/free
openclaw gateway restart
```

แล้ว probe:

```bash
openclaw models status --probe
```

## แบบฝึกหัดที่ 3: วิเคราะห์ error

ให้ดู error แล้วบอกวิธีแก้:

| Error | วิธีแก้ |
|---|---|
| 401 Missing Authentication header | Login OpenRouter ใหม่ |
| 402 Requires more credits | ใช้ free model หรือเติม credit |
| Context overflow | ลด prompt/output |
| web_search disabled | configure web provider |
| unknown command restart | ใช้ gateway restart |

---

# บทสรุป

การใช้ OpenClaw กับ OpenRouter Free Model เหมาะมากสำหรับการเรียน ทดลอง สอน และทำงานเบา เช่น Telegram brief, candidate discovery, classification และ prompt prototype

แต่ถ้าจะใช้งานจริงด้านกฎหมายหรือ HR Compliance ควรออกแบบเป็น 3 ชั้น:

```text
1) Discovery Mode: openrouter/free
2) Counsel Mode: model ที่มี context ใหญ่และมี credit เพียงพอ
3) Record Mode: append-only หลังอนุมัติ
```

หัวใจสำคัญคือ:

```text
ใช้ free model ให้ถูกงาน
ไม่ใช้ openrouter/auto ถ้าต้องการ free-only
ลด prompt
จำกัด output
ตรวจ logs
ไม่เปิดเผย secret
backup ก่อนแก้ config
```

---

# ภาคผนวก: Command Cheat Sheet

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

**End of Manual**

