# 🧠 Claude Memory System

> Give Claude a persistent memory — synced across machines, surviving every session close.

Claude is brilliant. But it forgets everything the moment you close the chat.  
Every new session? Blank slate. You re-explain your project, your preferences, your history. Every. Single. Time.

**This system fixes that.**

---

## 📋 Table of Contents

- [How It Works](#how-it-works)
- [Folder Structure](#folder-structure)
- [The Two Layers](#the-two-layers)
- [The Memory Cycle](#the-memory-cycle)
- [Setup Guide](#setup-guide)
- [Auto-Save Safety Net](#auto-save-safety-net)
- [Multi-Machine Sync](#multi-machine-sync)
- [The "done" Command](#the-done-command)
- [Scenario Coverage](#scenario-coverage)
- [File Templates](#file-templates)

---

## How It Works

The system solves Claude's fundamental statelessness using **two memory layers** stored as plain text files, synced between machines via Git.

```
Session Start  →  git pull  →  Load MEMORY.md  →  Read CLAUDE.md  →  Full context ✓
During Session →  Work with complete awareness of everything previously done
Session End    →  type "done"  →  Update files  →  git commit + push  →  Available everywhere
```

---

## Folder Structure

```
your-project/
├── CLAUDE.md                        ← Layer 2: deep session history & global rules
└── claude_memory/
│   ├── MEMORY.md                    ← master index, loaded first every session
│   ├── user_profile.md              ← who you are, preferences, communication style
│   └── project_status.md           ← current work state, what's done / pending
└── claude_settings/
    ├── settings.mac.json            ← Claude Code settings for Mac
    └── settings.linux.json         ← Claude Code settings for Ubuntu/Linux
```

---

## The Two Layers

### Layer 1 — `claude_memory/` : Working Memory

Think of this as the **briefing document** — fast, dense, structured facts.

| File | Purpose |
|---|---|
| `MEMORY.md` | Master index — Claude reads this first, then knows where everything else lives |
| `user_profile.md` | Who you are: role, preferences, communication style, tools |
| `project_status.md` | Current work: what's active, what's done, what's blocked |

**Why it works:**
- Each file is single-purpose and short — low token cost
- Structured format means Claude extracts facts instantly
- Stays lean forever — you update facts, not append history

---

### Layer 2 — `CLAUDE.md` : Long-Term Episodic Memory

Think of this as the **engineering journal** — a chronological record of what actually happened.

**What it stores:**
- Every bug solved (so it's never debugged twice)
- Every architectural decision with its reasoning
- Every pattern discovered
- Global rules that apply to every session

**Example entry:**
```markdown
## Session 2026-04-16
### Done
- Fixed OWL getAttachment() silent failure
  Root cause: missing explicit binding in setup() — not a props issue

### Decisions Made
- Keeping attachment logic in widget layer, not controller

### Patterns Discovered
- In Odoo 17 OWL, always verify component lifecycle order before debugging props
```

---

### Why Both Layers Must Coexist

| Scenario | Layer 1 only | Layer 2 only | Both |
|---|---|---|---|
| "What stack am I using?" | ✅ instant | ❌ must scan journal | ✅ |
| "Why did we avoid raw SQL?" | ❌ not stored | ✅ in session log | ✅ |
| "What's the current module status?" | ✅ structured fact | ❌ buried in history | ✅ |
| "What caused that OWL bug last month?" | ❌ too detailed | ✅ full context | ✅ |
| Token efficiency | ✅ lean | ❌ grows over time | ✅ load L1 always, L2 selectively |

---

## The Memory Cycle

```
┌─────────────────────────────────────────────────────────┐
│                     SESSION START                       │
│                                                         │
│   git pull ai_001                                       │
│         ↓                                               │
│   Load MEMORY.md  (quick context — who, where, what)    │
│         ↓                                               │
│   Read CLAUDE.md  (deep history — decisions & patterns) │
│         ↓                                               │
│   Claude has full context ✓                             │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│                    DURING SESSION                       │
│                                                         │
│   Work with complete awareness of:                      │
│   • Your identity & preferences                         │
│   • Current project state                               │
│   • All past decisions & patterns                       │
│   • All previously solved bugs                          │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│              SESSION END  (type "done")                 │
│                                                         │
│   Update CLAUDE.md  ← new learnings, bugs, decisions    │
│         ↓                                               │
│   Update claude_memory/ files if state changed          │
│         ↓                                               │
│   git commit + push                                     │
│         ↓                                               │
│   Available on all machines via git pull ✓              │
└─────────────────────────────────────────────────────────┘
```

---

## Setup Guide

### Step 1 — Create the folder structure

```bash
cd /your/project
mkdir -p claude_memory claude_settings
```

### Step 2 — Create `claude_memory/user_profile.md`

```bash
cat > claude_memory/user_profile.md << 'EOF'
# User Profile

- **Name:** Your Name
- **Role:** Your Role
- **Stack:** Your Stack
- **Machines:** Machine 1 + Machine 2
- **Language:** Your preferred language(s)
- **Communication style:** Direct, technical, no fluff
- **Tools:** Your tools
EOF
```

### Step 3 — Create `claude_memory/project_status.md`

```bash
cat > claude_memory/project_status.md << 'EOF'
# Project Status

## Active Module / Feature
- Name:
- Branch:
- Status: In progress

## Currently Working On
- [ ] Update this at the end of every session

## Recently Completed
- [ ] Update this at the end of every session

## Blocked / Waiting
- None
EOF
```

### Step 4 — Create `claude_memory/MEMORY.md`

```bash
cat > claude_memory/MEMORY.md << 'EOF'
# Memory Index — Load This First

> ⚠️ Always end sessions with "done" to trigger memory sync + git push

## Files to read at session start (in order):
1. `claude_memory/user_profile.md`     — who you are
2. `claude_memory/project_status.md`  — current work status
3. `CLAUDE.md`                         — full session history and technical decisions

## Global Rules
- Never use raw SQL — always use ORM
- Always explain the "why" before code blocks
- When "done" is typed: update CLAUDE.md, update memory files, then git commit + push
EOF
```

### Step 5 — Create `CLAUDE.md`

```bash
cat > CLAUDE.md << 'EOF'
# CLAUDE.md — Session Log & Technical History

## Global Rules (Apply to ALL sessions)
At the START of every session, do the following steps IN ORDER — automatically, no permission needed:
1. Run `git pull` and capture the output
2. Read `claude_memory/MEMORY.md` and all files it references
3. Read this file (CLAUDE.md)
4. Output as your very first line:
   `📥 pull: <result> | 📖 CLAUDE.md synced`

## When "done" is typed — do this automatically:
1. Append a new session entry below under ## Session Log
2. Update `claude_memory/project_status.md` to reflect current state
3. Run: `git add -A && git commit -m "memory: update after session $(date +%Y-%m-%d)" && git push`
4. Confirm with: `✅ Memory saved & pushed`

---

## Session Log

### Session — YYYY-MM-DD — System Initialized
- Two-layer memory system created
- Layer 1: claude_memory/ (structured fast index)
- Layer 2: CLAUDE.md (deep session history)
- Git sync configured
EOF
```

### Step 6 — Commit and push

```bash
git init   # skip if repo already exists
git add -A
git commit -m "feat: initialize two-layer Claude memory system"
git push
```

---

## Auto-Save Safety Net

The `done` command is the preferred path — but life happens. You close a tab, get distracted, session crashes.

The Stop hook in Claude Code catches all of those automatically.

### Mac — `claude_settings/settings.mac.json`

```json
{
  "hooks": {
    "Stop": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "cd ~/Documents/your-project && git status --porcelain | grep -q . && git add . && git commit -m 'auto-save: session closed without done' && git push && echo '{\"systemMessage\": \"🔄 auto-saved and pushed (session closed without done)\"}' || true"
          }
        ]
      }
    ]
  }
}
```

### Ubuntu — `claude_settings/settings.linux.json`

```json
{
  "hooks": {
    "Stop": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "cd ~/your-project && git status --porcelain | grep -q . && git add . && git commit -m 'auto-save: session closed without done' && git push && echo '{\"systemMessage\": \"🔄 auto-saved and pushed (session closed without done)\"}' || true"
          }
        ]
      }
    ]
  }
}
```

### Activate on each machine

**Mac:**
```bash
cp claude_settings/settings.mac.json ~/.claude/settings.json
```

**Ubuntu:**
```bash
cp claude_settings/settings.linux.json ~/.claude/settings.json
```

---

## Multi-Machine Sync

Using Git as the sync mechanism is the right choice because:

- ✅ No extra infrastructure — no database, no API, no sync service
- ✅ History is auditable — see how memory evolved over time
- ✅ Works offline — work locally and push when reconnected
- ✅ Free backup — the repo is the backup
- ✅ Conflict-safe — `.md` merge conflicts are human-readable

**On the second machine, just run:**
```bash
git clone https://github.com/your-username/your-repo.git
cp claude_settings/settings.linux.json ~/.claude/settings.json
```

---

## The "done" Command

Type `done` at the end of any session. Claude will automatically:

1. Append a dated entry to the Session Log in `CLAUDE.md`
2. Update `claude_memory/project_status.md`
3. Run `git add -A && git commit && git push`
4. Confirm: `✅ Memory saved & pushed`

**How to distinguish commit types in `git log`:**

```
a1b2c3d  memory: update after session 2026-04-16   ← typed "done" ✅
b87a17d  auto-save: session closed without done    ← tab closed 🔄
```

---

## Scenario Coverage

| Scenario | Result |
|---|---|
| Type `done` normally | ✅ CLAUDE.md updated with session log, then pushed |
| Close tab / forget `done` | ✅ Stop hook auto-commits and pushes silently |
| Distraction / session ends unexpectedly | ✅ Auto-saved on close |
| Hard crash / power outage | ❌ Unavoidable — nothing can catch this |

---

## File Templates

All templates are ready to copy from the [Setup Guide](#setup-guide) above.

| File | Purpose | Update frequency |
|---|---|---|
| `CLAUDE.md` | Session log + global rules | Every session (auto) |
| `claude_memory/MEMORY.md` | Index + global rules | Rarely |
| `claude_memory/user_profile.md` | Your identity & preferences | Rarely |
| `claude_memory/project_status.md` | Current work state | Every session (auto) |
| `claude_settings/settings.mac.json` | Stop hook for Mac | Once |
| `claude_settings/settings.linux.json` | Stop hook for Ubuntu | Once |

---

## The Result

Claude went from a brilliant stranger that needed re-introducing every day — to a collaborator that picks up exactly where you left off, every single time.

**No more repetition. No more lost context. No more starting from zero.**

The memory lives in plain text files. It syncs via Git. It costs nothing extra.

---

*Built by [@Mohamed0halim](https://github.com/Mohamed0halim)*  

#Claude #AI #DeveloperTools #Productivity #Automation
