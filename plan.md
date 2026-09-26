Bilkul bhai. Isko main **ek master prompt + phase-wise execution plan** mein compress kar deta hoon, jise tu ChatGPT/Cursor/Claude ko deke project ke saath-saath seekh sakta hai.

## 🧠 Master Prompt

I want to build a production-style AI Engineering Agent called **DevPilot** without taking any course.

Goal:

Build an AI agent that can investigate software engineering issues using:

* company/project documentation
* source code
* Jira/issues
* GitHub
* logs
* database

The agent should understand an issue, retrieve relevant context, use tools to investigate, form a hypothesis, propose a fix, run tests, and after human approval create a PR.

Technologies I want to learn through this project:

* LLMs
* AI Agents
* Tool Calling
* MCP
* RAG
* Embeddings
* Vector Databases
* LangChain
* LangGraph
* n8n

Preferred stack:

* Python for AI/Agent service
* Java + Spring Boot for backend APIs
* React for UI
* PostgreSQL + pgvector
* Docker

Learning rule:
DO NOT teach me theory like a course.
Teach me concepts only when they become necessary for the current implementation.

For every phase:

1. Explain the minimum concept I need.
2. Explain why we need it in DevPilot.
3. Show the architecture.
4. Give me a small implementation task.
5. Let me implement it.
6. Review/improve my implementation rather than rewriting everything.
7. Then move to the next phase.

Do not dump the entire project code at once.
Do not hide abstractions behind frameworks.
Whenever using LangChain, LangGraph or MCP, first explain what is happening underneath.

The final architecture should roughly become:

React
↓
Spring Boot API
↓
AI Agent Service
↓
LangGraph
├── RAG → PostgreSQL + pgvector
├── MCP → GitHub/Jira/Logs/DB tools
└── LLM

n8n should be used for external automation such as:
GitHub/Jira/Grafana → n8n → DevPilot → Slack/Jira

Eventually the agent should be able to:

Issue
→ understand issue
→ retrieve documentation
→ inspect source code
→ query tools
→ investigate
→ form hypothesis
→ propose fix
→ modify code in a sandbox
→ run tests
→ debug failures
→ ask for human approval
→ create PR

Start with Phase 1 only.
Do not jump ahead.

---

# 🔥 Phase-wise plan

### Phase 0 — Setup

**Goal:** Environment ready.

Learn/build:

* Python project
* LLM API
* basic tool calling
* Docker
* Postgres

**Output:**

```text
DevPilot/
├── agent/
├── backend/
├── frontend/
├── infra/
└── README.md
```

---

### Phase 1 — Your first Agent 🤖

**Learn:**

* LLM
* prompt
* tool
* tool calling
* agent loop

Build:

```text
User
 ↓
LLM
 ↓
"Need information"
 ↓
Tool
 ↓
Result
 ↓
LLM
 ↓
Answer
```

Start with stupidly simple tools:

```text
get_issue()
search_code()
```

**Don't use LangChain/LangGraph yet.**

You'll understand what an agent actually is.

---

### Phase 2 — RAG 📚

Give DevPilot your fake company knowledge:

```text
docs/
├── architecture.md
├── payment-service.md
├── database.md
├── deployment.md
└── incidents/
```

Learn:

```text
Documents
 ↓
Chunks
 ↓
Embeddings
 ↓
Vector DB
 ↓
Similarity search
 ↓
Relevant context
 ↓
LLM
```

Use:

**Postgres + pgvector**

Don't start with Pinecone/Qdrant/etc. yet.

---

### Phase 3 — LangChain 🦜

Now take what you manually built and introduce LangChain.

Learn:

* Document loaders
* embeddings
* retrievers
* tools
* structured output

The objective isn't:

> "I know LangChain."

It's:

> "I understand which manual code LangChain is abstracting."

---

### Phase 4 — MCP 🔌

Build your own MCP server.

Expose:

```text
get_jira_issue()
search_github()
get_file()
search_logs()
query_database()
```

Architecture:

```text
DevPilot Agent
      │
     MCP
      │
      ▼
 MCP Server
 ├── GitHub
 ├── Jira
 ├── Logs
 └── DB
```

At this point you'll understand:

**RAG = knowledge/context**

**MCP = live tools/capabilities**

That's a very important distinction.

---

### Phase 5 — LangGraph 🧠

Now your agent becomes a real workflow.

```text
START
  ↓
Understand Issue
  ↓
Retrieve Context
  ↓
Investigate
  ↓
Analyze
  ↓
Enough Evidence?
  ├── NO → Investigate Again
  │
  └── YES
       ↓
   Propose Fix
       ↓
    Run Tests
       ↓
   Tests Pass?
    ├── NO → Debug
    └── YES
         ↓
    Human Approval
         ↓
      Create PR
```

Learn:

* State
* Nodes
* Edges
* Conditional edges
* loops
* human-in-the-loop
* persistence

This is where the project becomes **properly agentic**.

---

### Phase 6 — Coding Agent 👨‍💻

Now give the agent a sandboxed Git repository.

Tools:

```text
read_file()
search_code()
modify_file()
run_tests()
git_diff()
```

Agent can now:

```text
Issue
 ↓
Find relevant code
 ↓
Understand code
 ↓
Modify code
 ↓
Run tests
 ↓
Read failure
 ↓
Modify again
 ↓
Run tests
```

**Sandbox this.**

Don't give an AI agent unrestricted access to your machine.

---

### Phase 7 — n8n ⚡

Only now add n8n.

Example:

```text
GitHub PR
    ↓
   n8n
    ↓
DevPilot
    ↓
Code analysis
    ↓
Slack
```

Another:

```text
Grafana Alert
     ↓
    n8n
     ↓
DevPilot
     ↓
Investigate incident
     ↓
Slack
```

Here you'll understand where **workflow automation ends and agentic reasoning begins**.

---

# 🏁 Final Project

Eventually:

```text
                       ┌─────────────┐
                       │    React    │
                       └──────┬──────┘
                              ↓
                       ┌─────────────┐
                       │Spring Boot  │
                       └──────┬──────┘
                              ↓
                       ┌─────────────┐
                       │  LangGraph  │
                       └──────┬──────┘
                              │
             ┌────────────────┼────────────────┐
             ↓                ↓                ↓
           RAG              MCP              LLM
             ↓                ↓
        pgvector       GitHub/Jira/DB
             
                              ↑
                             n8n
                              ↑
                   GitHub/Grafana/Jira
```

### And the killer demo:

You give it:

> `PAYMENT-1234: Payments returning 500 for some merchants`

And DevPilot responds:

```text
Investigation started.

✓ Read issue
✓ Retrieved payment architecture
✓ Found relevant service
✓ Inspected recent code changes
✓ Queried logs
✓ Identified probable root cause
✓ Proposed code change
✓ Modified code in sandbox
✓ Tests passed

⚠ Human approval required

Proposed PR:
"Fix merchant validation in PaymentService"
```

**Bhai, is project ko complete karne ke baad tu definitions nahi rat raha hoga — tu literally Agent + RAG + Vector DB + MCP + LangChain + LangGraph + n8n ek hi system mein use kar chuka hoga.**
