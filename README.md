# Build Your First AI Agent

You're here because you just sat through the **"Day in the Life of a Solutions Architect"** session. This repo is what comes next — you're going to build something real.

Not a tutorial app. Not a demo. An actual AI **agent**: a program that reasons, decides which tools to call, calls them, reads the results, and keeps going until it has an answer. This exact pattern is what's powering every AI product you've heard of in the last 12 months.

By the end of this exercise (30 minutes), you'll have:
- A running agent loop you wrote and understand
- Two tools the model can call and chain together
- A guardrail that keeps the agent from going off the rails
- Code committed to a repo you own

**This is the same architecture you'll use for your capstone.** The scale will be different. The muscles are identical.

---

## What you're actually building (read this first)

Most people think AI is a black box. It's not. Under every "AI agent" is a loop that looks like this:

```
user message
     ↓
  model thinks
     ↓
  "I need to call a tool"  →  your code runs the tool  →  result goes back to model
     ↓
  model thinks again
     ↓
  final answer
```

The model doesn't run your tools — **you do.** The model just decides *when* to ask for one and *what arguments* to pass. Your code is the one actually executing things and handing results back. Once that clicks, the whole space of "AI agents" becomes much less mysterious.

What you're building today:
- `agent.py` — the loop itself. Sends messages to the model, checks if it wants to call a tool, runs the tool, loops back.
- `tools.py` — the actual tool functions. Just normal Python. The model never sees this code, only the description you write for it.

That's genuinely it.

---

## Setup

You need **Python 3.10+** and an API key. We're using **Grok (xAI)** — it's free, no credit card, takes 2 minutes to get a key.

**Step 1: Get your API key**

Go to [console.x.ai](https://console.x.ai) → sign in → create an API key. Copy it. You'll need it in a moment.

> **No key right now?** That's fine. Set `USE_MOCK=1` in your `.env` and the agent will run with a fake model. You won't get real AI responses but the loop still works and the architecture lesson is the same. Come back and swap in a real key when you have one.

**Step 2: Clone and install**

```bash
git clone https://github.com/omar2000751/DayintheLifeSA.git agent-starter
cd agent-starter

python -m venv .venv
source .venv/bin/activate     # Windows: .venv\Scripts\activate

pip install -r requirements.txt
```

**Step 3: Wire up your key**

```bash
cp .env.example .env
```

Open `.env` and paste your key into `XAI_API_KEY`. Leave everything else as-is.

**Step 4: Verify it works**

```bash
python agent.py "what should I pack for Boston today?"
```

You should see the agent call the `get_weather` tool, get a result back, and give you a final answer. If you see that tool call printed — you're good, the loop is working.

> **Using OpenAI instead of Grok?** Set `OPENAI_API_KEY` and change `PROVIDER=openai` in your `.env`. The rest of the code is identical — Grok uses the same API format.

---

## The exercise

You're going to use **Claude Code** to extend this agent, not hand-type everything from scratch. That's intentional — the skill we're building is knowing *what* to ask an AI to build, not grinding out boilerplate. Open Claude Code in this folder (`claude .`) and follow the steps below. Each one has a prompt you can paste directly.

---

### Step 1 — Understand the loop (8 min)

Before you change anything, you need to know what's actually happening.

> **Paste this into Claude Code:**
> *"Open agent.py and walk me through the agent loop line by line. Explain what happens when the model decides to call a tool versus when it gives a final answer. Then run it with the question 'what should I pack for Boston today?' and show me the tool call in the output."*

**What to look for:** You should see `-> calling tool: get_weather({'city': 'Boston'})` printed before the final answer. That's the moment your code intercepted the model's tool request, ran your Python function, and handed the result back. That's the whole game.

---

### Step 2 — Add a tool that writes (8 min)

Right now the agent can only *read* (fake weather data). Make it write too.

> **Paste this into Claude Code:**
> *"Add a second tool called save_note(text) that appends a timestamped line to notes.txt. Add it to TOOL_SCHEMAS and TOOL_FUNCTIONS in tools.py. Then ask the agent: 'Check the weather in Boston and save a one-line summary to my notes.' Show me the notes.txt output."*

**What to look for:** The agent should call `get_weather` first, then call `save_note` with the result. Two tool calls, chained automatically. The model decided the order — you didn't program that logic.

---

### Step 3 — Add guardrails (7 min)

A capable agent without guardrails is a liability. There are two kinds of guardrails and they live in different places for a reason.

> **Paste this into Claude Code:**
> *"Add two guardrails: (1) the agent should politely refuse any request that isn't about weather or notes — put this in the system prompt, (2) save_note should hard-reject any text longer than 500 characters with an error — put this in the Python function. After implementing both, explain to me in a comment why each guardrail lives where it does."*

**What to look for:** This is the real lesson. Behavioral rules ("be helpful but stay on topic") belong in the prompt because they're fuzzy and contextual. Hard limits ("never write more than 500 chars") belong in code because the model should never be able to talk its way around them.

---

### Step 4 — Ship it (5 min)

> **Paste this into Claude Code:**
> *"Fill in the 'What I built' section of the README with a 3-4 sentence description of this agent — what it does, what tools it has, and what guardrails protect it. Then commit everything to git with a clear message."*

You built and shipped an agent. That's not nothing.

---

## Stretch goals

Done early? Pick one:

- **Connect a real weather API.** Replace the hardcoded `get_weather` with a call to [wttr.in](https://wttr.in) (no key needed: `curl wttr.in/Boston?format=3`). Now your agent has real data.
- **Add an MCP server.** Run `claude mcp add fetch -- uvx mcp-server-fetch` and ask the agent to read a webpage. You just gave it a tool you didn't write.
- **Write a tool of your own.** Dice roller, unit converter, file reader — anything. Add the schema and the function. Ask the agent to use it.
- **Package it as a Claude Code skill.** The `.claude/skills/` folder is already set up. Flesh out the research-agent skill so it can be invoked as a slash command.

---

## What I built

_(You'll fill this in at Step 4.)_

---

## File map

```
agent-starter/
├── agent.py             ← the loop: sends messages, runs tools, loops back
├── tools.py             ← tool functions + their schemas (what the model sees)
├── requirements.txt     ← openai SDK + dotenv, that's it
├── .env.example         ← copy to .env, add your key
└── .claude/
    └── skills/
        └── research-agent/   ← stretch goal skill starter
```
