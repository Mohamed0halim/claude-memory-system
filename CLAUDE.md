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
