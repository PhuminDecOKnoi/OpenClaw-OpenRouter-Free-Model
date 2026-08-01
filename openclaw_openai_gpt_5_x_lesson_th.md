# Lesson Plan: OpenClaw + OpenAI GPT-5.x for IT Instructors

> This English (US) lesson plan is designed for experienced IT instructors who need to teach university students how to understand, configure, and demonstrate OpenClaw with OpenAI GPT-5.x and OpenRouter as an optional routing layer.  
> Code examples use explicit syntax-highlight language tags and Thai `# XXX:` teaching comments for classroom explanation.

---

## 0. Metadata

| Item | Description |
|---|---|
| Lesson title | OpenClaw + OpenAI GPT-5.x for Practical AI-Agent Operation |
| Language | English (US), with Thai inline teaching comments in code blocks |
| Target learners | IT, CS, Software Engineering, Digital Business, and AI-application students |
| Instructor profile | Experienced IT trainer familiar with APIs, CLI, web applications, automation, or AI tools |
| Suggested duration | 2.5–3 hours, or two class sessions |
| Format | Lecture + demo + lab + discussion |
| Level | Intermediate to advanced beginner |
| Updated | August 1, 2026 |

---

## 1. Learning Objectives

By the end of this lesson, learners should be able to:

1. Explain OpenClaw as an AI-agent gateway.
2. Distinguish model providers, model refs, runtimes, tools, and channels.
3. Describe how OpenClaw can connect to OpenAI and OpenRouter.
4. Choose a model strategy for reasoning, coding, low-cost workloads, and automation.
5. Design prompts for summarization, web search, file workflows, and classification.
6. Identify risks related to API keys, tokens, cost, rate limits, context overflow, and tool permissions.
7. Design a safe lab or assignment without exposing real secrets.

---

## 2. Executive Summary for Instructors

OpenClaw should be taught as a practical **agent gateway** rather than as a language model. The model is only one component of the system. A complete workflow also includes user channels, routing, tools, permissions, logging, and operational controls.

When using OpenAI GPT-5.x in class, instructors should focus on the following learning themes:

- AI-agent architecture
- Model-provider selection
- Responses-style workflows
- Function calling and structured outputs
- Tool permissions
- Prompt design
- Cost control
- Security and governance

OpenRouter can be introduced as an optional routing layer that allows the same teaching pattern to extend beyond one provider. Students should understand that provider routing is an architectural choice, not just a command-line setting.

---

## 3. Concept Map: OpenClaw + OpenAI + OpenRouter

```text
# XXX: แผนภาพนี้ใช้เปิดบทเรียน เพื่อให้ผู้เรียนเห็นว่า agent workflow มีหลายชั้น ไม่ใช่มีแค่ model
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

### Instructor Note

Start by asking students this question:

```text
# XXX: คำถามนี้ช่วยแยกความเข้าใจระหว่าง model กับระบบ agent รอบ ๆ model
If GPT is the model, what parts of the system are not the model?
```

Expected answers include gateway, tools, channels, files, prompts, permissions, logs, and scheduled jobs.

---

## 4. Core Vocabulary

| Term | Teaching Definition |
|---|---|
| **AI Agent** | A system that can use a model plus tools, memory/context, and instructions to complete a task. |
| **Gateway** | The OpenClaw component that routes user requests to models and tools. |
| **Model Provider** | A provider such as OpenAI, OpenRouter, Anthropic, Gemini, or a local endpoint. |
| **Model Ref** | A provider-qualified model reference, such as `openai/<model>` or `openrouter/<provider>/<model>`. |
| **Tool Call** | A structured request from the model or agent to use an external capability. |
| **Structured Output** | A response constrained to a defined schema such as JSON. |
| **Cron Automation** | A scheduled task that runs automatically at a defined time. |
| **Context Window** | The maximum amount of input and output the model can handle in one interaction. |
| **Token Hygiene** | The practice of protecting API keys, tokens, credentials, and logs. |

---

## 5. Teaching Model: Three-Layer Explanation

Use this three-layer model to make the system easy to understand.

### Layer 1: User Experience

Students interact through a channel:

- Dashboard
- Telegram
- CLI
- Other messaging channels

### Layer 2: Agent Orchestration

OpenClaw receives the request, maintains the session, applies configuration, and decides which model or tool path to use.

### Layer 3: Model and Tool Execution

The selected provider returns the answer or triggers tool-related workflows such as search, file access, function calling, or automation.

---

## 6. Instructor Demonstration Sequence

Recommended sequence:

1. Show the architecture diagram.
2. Open the terminal and check OpenClaw status.
3. Open the Dashboard.
4. Authenticate a provider.
5. List available models.
6. Set a primary model and a fallback.
7. Run a short prompt.
8. Show the difference between a normal answer and a tool-augmented answer.
9. Create a lightweight Cron task.
10. Close with security and cost-control rules.

---

## 7. Setup Commands

### 7.1 Install or Verify OpenClaw

```bash
# XXX: ตรวจเวอร์ชันและสุขภาพระบบก่อนสอน เพื่อให้รู้ว่าเครื่องพร้อมใช้หรือไม่
openclaw --version
openclaw doctor
openclaw gateway status
```

If installation is needed:

```bash
# XXX: ติดตั้ง OpenClaw แบบ global ผ่าน npm สำหรับเครื่องที่มี Node.js พร้อมแล้ว
npm install -g openclaw@latest

# XXX: onboarding พร้อม daemon ช่วยให้ gateway ทำงานเป็นบริการเบื้องหลัง
openclaw onboard --install-daemon
```

### 7.2 Open the Dashboard

```bash
# XXX: เปิด Dashboard เพื่อแสดงสถานะระบบผ่าน UI ให้ผู้เรียนเห็นภาพรวม
openclaw dashboard
```

Or open:

```bash
# XXX: เปิด Dashboard ด้วย local URL โดยตรงบน macOS หาก command dashboard ไม่เปิด browser อัตโนมัติ
open http://127.0.0.1:18789
```

### 7.3 Authenticate a Provider

OpenAI example:

```bash
# XXX: login provider OpenAI เพื่อใช้โมเดลตระกูล GPT ผ่าน OpenClaw
openclaw models auth login --provider openai
```

OpenRouter example:

```bash
# XXX: login provider OpenRouter เพื่อให้ OpenClaw route ไปยังโมเดลผ่าน OpenRouter ได้
openclaw models auth login --provider openrouter
```

### 7.4 Check Model Status

```bash
# XXX: ตรวจสถานะ model และ probe เพื่อยืนยันว่าเรียก provider ได้จริง
openclaw models status
openclaw models status --probe

# XXX: list model แยกตาม provider เพื่อสอนเรื่อง provider-qualified model refs
openclaw models list --provider openai
openclaw models list --provider openrouter
```

---

## 8. Model Strategy for Teaching

### 8.1 Model Selection Logic

| Workload | Recommended Strategy |
|---|---|
| Short explanation | Use a lightweight model. |
| Reasoning-heavy analysis | Use a reasoning-capable model. |
| Coding demo | Use a model strong at code generation and debugging. |
| Daily automation | Use a low-cost or free model where accuracy requirements are modest. |
| Sensitive or high-risk output | Use stronger controls, shorter context, and human review. |

### 8.2 Classroom Rule

For public classroom demos:

```text
# XXX: กฎนี้ใช้ป้องกันความเสี่ยงด้านข้อมูลลับ ค่าใช้จ่าย และการใช้ context เกินจำเป็น
Use test accounts.
Use demo keys only.
Keep outputs short.
Avoid confidential data.
Avoid full-document uploads.
Use verified model refs.
```

---

## 9. Prompt Engineering Pattern

Use this prompt structure for labs:

```text
# XXX: โครง prompt นี้ช่วยให้ผู้เรียนระบุบทบาท งาน input ข้อจำกัด และรูปแบบผลลัพธ์ได้ชัดเจน
Role:
You are ...

Task:
Do ...

Input:
Use the following data ...

Constraints:
- Answer in English.
- Use no more than 500 words.
- Do not call external tools unless instructed.
- If information is missing, say so.

Output format:
Use headings and bullet points.
```

### Example Prompt

```text
# XXX: prompt ตัวอย่างนี้ใช้สอนความแตกต่างระหว่าง AI model กับ AI agent gateway แบบไม่ใช้ web search
Role:
You are a teaching assistant for an IT course.

Task:
Explain the difference between an AI model and an AI agent gateway.

Constraints:
- Answer in English.
- Use no more than six bullet points.
- Use beginner-friendly language.
- Do not use web search.

Output format:
Bullet points only.
```

---

## 10. Demonstration: From Simple Prompt to Agent Workflow

### Step 1: Simple Prompt

```text
# XXX: เริ่มจาก prompt สั้นเพื่อให้เห็น baseline answer ก่อนเพิ่มข้อจำกัด
Explain what OpenClaw does in five bullet points.
```

### Step 2: Constrained Prompt

```text
# XXX: เพิ่ม audience และข้อห้าม เพื่อให้ผลลัพธ์เหมาะกับผู้เรียนปี 1 มากขึ้น
Explain what OpenClaw does in five bullet points for first-year IT students.
Avoid marketing language.
```

### Step 3: Tool-Aware Prompt

```text
# XXX: prompt นี้สอนให้ agent ตรวจสถานะระบบก่อนสรุป readiness สำหรับ demo
Check the latest configured model status first.
Then explain whether the system is ready for a classroom demo.
Keep the answer concise.
```

### Step 4: Scheduled Workflow Prompt

```text
# XXX: prompt นี้ใช้สอนแนวคิด automation และ cost control ก่อนตั้ง cron จริง
Create a daily 8:00 AM brief.
Use a lightweight model.
Limit the response to three items.
Ask for confirmation before running any expensive task.
```

---

## 11. Lab 1: Architecture Mapping

### Goal

Students map the OpenClaw architecture and identify which component is responsible for each action.

### Student Task

Draw a diagram showing:

- User interface
- OpenClaw Gateway
- Agent session
- Model provider
- Tool layer
- Output layer

### Evaluation Criteria

| Criteria | Points |
|---|---:|
| Correctly identifies the gateway | 2 |
| Distinguishes model and provider | 2 |
| Includes tools and outputs | 2 |
| Explains the flow clearly | 2 |
| Uses correct terminology | 2 |

---

## 12. Lab 2: Model Ref and Provider Routing

### Goal

Students understand the difference between a provider model slug and an OpenClaw model ref.

### Student Task

Classify each example:

| Example | Classification |
|---|---|
| `openai/<model>` | OpenAI model ref |
| `openrouter/free` | OpenRouter free router pattern |
| `openrouter/<provider>/<model-id>:free` | OpenRouter provider-qualified model ref |
| `<provider>/<model-id>:free` | Provider model slug, not necessarily a full OpenClaw ref |

### Discussion Question

Why is an exact model ref safer than an ambiguous model name in a workshop?

---

## 13. Lab 3: Safe Prompt Design

### Goal

Students design prompts that control scope, cost, and risk.

### Student Task

Write a prompt for a daily brief that:

- Answers in English
- Uses no more than 500 words
- Lists no more than three items
- Does not read full PDFs
- Includes sources only if available
- States clearly when information is insufficient

### Instructor Feedback Points

- Is the task clear?
- Are constraints measurable?
- Is tool usage controlled?
- Is the output format specified?
- Does the prompt avoid unnecessary cost?

---

## 14. Lab 4: Troubleshooting Simulation

### Scenario A

```text
# XXX: error นี้ใช้ฝึกตรวจ model ref, provider และ catalog ปัจจุบัน
Error: Unknown model
```

Expected checks:

1. Run `openclaw models list --provider <provider>`.
2. Confirm the model ref format.
3. Check whether the model exists in the current catalog.
4. Restart the gateway after configuration changes.
5. Run `openclaw models status --probe`.

### Scenario B

```text
# XXX: error นี้ใช้ฝึกตรวจ provider auth และ environment/config ที่เกี่ยวข้อง
Error: Missing authentication
```

Expected checks:

1. Confirm the provider is authenticated.
2. Re-run provider login.
3. Check environment variables or config files.
4. Restart the gateway.
5. Avoid displaying real keys during troubleshooting.

### Scenario C

```text
# XXX: error นี้ใช้สอนการลด prompt/history/tool output เมื่อ context ใหญ่เกินไป
Context overflow
```

Expected fixes:

1. Start a new session.
2. Reduce prompt size.
3. Read only the required part of a file.
4. Split the task into stages.
5. Limit output length.

---

## 15. Security Module

Security should be taught as a required part of AI-agent operation, not as an optional topic.

### Never Share

```text
# XXX: รายการนี้คือข้อมูลลับหรือข้อมูลอ่อนไหว ห้ามแสดงบนจอ ห้าม commit และห้ามส่งใน chat สาธารณะ
API keys
Gateway tokens
Telegram bot tokens
Passwords
Session tokens
.env files
Auth profiles
Logs containing secrets
```

### Safer Practice

- Use demo accounts.
- Use disposable keys for workshops.
- Rotate keys after public demonstrations.
- Sanitize logs before sharing.
- Avoid student data or customer data in prompts.
- Do not commit secrets to GitHub.

### Instructor Demonstration

Show a fake key pattern only:

```text
# XXX: ตัวอย่าง key ปลอมสำหรับ slide/demo เท่านั้น ไม่ใช่ key จริง
sk-or-v1-REDACTED_EXAMPLE_ONLY
```

Never show a real API key.

---

## 16. Cost-Control Module

Cost control is part of responsible AI operations.

### Cost Drivers

- Long prompts
- Long outputs
- Large files
- Repeated tool calls
- Frequent Cron jobs
- Expensive models
- Unbounded web search

### Cost-Safe Classroom Defaults

```text
# XXX: ค่า default นี้ช่วยลดความเสี่ยงค่าใช้จ่ายระหว่าง workshop และ lab
Output length: short
Tool calls: minimal
Cron frequency: low
Model: verified low-cost or free model
Files: small samples only
Session: new session for each lab
```

---

## 17. Suggested Three-Hour Teaching Plan

| Time | Segment | Instructor Action | Student Output |
|---|---|---|---|
| 0:00–0:15 | Opening | Explain the goal and show architecture. | Lesson expectations. |
| 0:15–0:35 | Concepts | Define gateway, provider, model ref, and tool. | Concept notes. |
| 0:35–1:00 | Setup | Verify OpenClaw and open Dashboard. | Working local status check. |
| 1:00–1:25 | Provider authentication | Demonstrate provider login safely. | Understanding of token handling. |
| 1:25–1:50 | Model strategy | Show model listing, set, fallback, and probe. | Model strategy table. |
| 1:50–2:15 | Prompt lab | Run controlled prompts. | Prompt draft. |
| 2:15–2:40 | Automation | Explain and design a Cron task. | Cron design draft. |
| 2:40–2:55 | Troubleshooting | Review common errors. | Troubleshooting checklist. |
| 2:55–3:00 | Wrap-up | Reinforce security and cost controls. | Key takeaways. |

---

## 18. Slide Outline

1. What is an AI agent?
2. What is OpenClaw?
3. OpenClaw architecture
4. Model providers and model refs
5. OpenAI GPT-5.x concept
6. OpenRouter as an optional routing layer
7. Tools: Web Search, File Search, Cron, local files
8. Prompt pattern
9. Security and API-key hygiene
10. Cost and rate-limit control
11. Troubleshooting playbook
12. Student lab instructions
13. Assignment and rubric
14. Summary and Q&A

---

## 19. Quiz

### Multiple Choice

1. What is OpenClaw in this lesson?
   - A. A spreadsheet application
   - B. An AI-agent gateway
   - C. A database server
   - D. A password manager

2. What is a model provider?
   - A. A service that supplies or routes AI models
   - B. A file browser
   - C. A keyboard shortcut
   - D. A Markdown parser

3. Why should instructors avoid showing real API keys?
   - A. They are visually distracting
   - B. They may expose account access and billing risk
   - C. They make the terminal slower
   - D. They are not compatible with Markdown

4. What is context overflow?
   - A. A styling issue
   - B. An authentication method
   - C. A failure caused by too much input/output for the model context window
   - D. A provider dashboard

### Short Answer

1. Explain the difference between a model and an agent gateway.
2. Give two examples of unsafe token handling.
3. Give two ways to reduce cost in a scheduled AI-agent task.
4. Explain why exact model refs are safer than ambiguous model names.

---

## 20. Assignment

### Assignment Title

Design a Safe AI-Agent Workflow with OpenClaw

### Student Deliverables

Students must submit:

1. An architecture diagram.
2. A model strategy table.
3. A safe prompt template.
4. A proposed Cron automation design.
5. A security checklist.
6. A short troubleshooting plan.

### Rubric

| Criteria | Points |
|---|---:|
| Architecture accuracy | 20 |
| Model strategy clarity | 20 |
| Prompt design quality | 20 |
| Security controls | 20 |
| Troubleshooting plan | 10 |
| Professional formatting | 10 |
| **Total** | **100** |

---

## 21. Instructor Checklist

Before teaching:

- [ ] Verify OpenClaw installation.
- [ ] Verify provider authentication.
- [ ] Confirm model availability.
- [ ] Test `openclaw models status --probe`.
- [ ] Prepare fake keys for slides.
- [ ] Remove all real secrets from screenshots.
- [ ] Prepare backup prompts.
- [ ] Prepare at least one troubleshooting scenario.
- [ ] Keep all sample files small and non-sensitive.

---

## 22. Key Takeaways

- OpenClaw is an AI-agent gateway, not the model itself.
- OpenAI and OpenRouter are model-provider options.
- Exact model refs reduce configuration errors.
- Tool usage must be controlled.
- Prompt design affects cost, safety, and reliability.
- API keys and tokens must never be exposed.
- Cron automation should be short, scoped, and cost-aware.
- Students should learn both the command flow and the operational reasoning behind it.

---

## 23. External References

- OpenClaw documentation: https://docs.openclaw.ai/
- OpenClaw Models CLI: https://docs.openclaw.ai/cli/models
- OpenClaw model providers: https://docs.openclaw.ai/concepts/model-providers
- OpenAI API documentation: https://platform.openai.com/docs
- OpenAI model documentation: https://platform.openai.com/docs/models
- OpenRouter documentation: https://openrouter.ai/docs
- OpenRouter Quickstart: https://openrouter.ai/docs/quickstart

---

## Summary

This lesson plan helps IT instructors teach OpenClaw, OpenAI GPT-5.x, and OpenRouter through a practical, security-first, and operations-focused approach. The lesson emphasizes architecture, model strategy, prompt design, tool control, cost awareness, troubleshooting, and student lab design.
