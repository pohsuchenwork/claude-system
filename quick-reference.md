# Claude Code: Quick Reference

## The structure (what lives where)

**Inside the project folder:**

| Thing | Where | What it's for |
|---|---|---|
| **Static memory** | `CLAUDE.md` | The rules that don't change: stack, structure, conventions, guardrails, commands, plus a short "when compacting, preserve" note. Auto-loads every session. Keep it under ~200 lines. |
| **Dynamic memory** | `DYNAMIC.md` | The current state that changes: what's in progress, decisions, gotchas, where you left off. Read every session (via a line in CLAUDE.md). |
| **Commands** | `.claude/commands/` | Your reusable slash commands (one `.md` file each). *Commands and skills are unified; commands are the simplest form (full explanation in the explainer).* |

**The mental model:** CLAUDE.md = how the project *works* (permanent). DYNAMIC.md = where things *stand* (current). Git = what *happened* (history).

---

## The commands

*Every command takes an optional argument (shown in brackets) to scope what it does; run it bare for the default.*

**Maintenance**
- `/save [optional: note]` - update DYNAMIC.md to the current state (run at every stopping point)
- `/commit [optional message]` - show the branch, commit with your message or an auto-generated one, confirm first

**Understanding & delegation**
- `/diffs [optional: file/area]` - show what changed since the last commit, and explain it
- `/explain [anything]` - explain code, a concept, a question, or an attached file/image
- `/subagent [task]` - hand a task (usually big exploration) to a *subagent* (a separate temporary Claude that works in its own memory and reports back a summary); it picks a suitable model, uses one by default, reports back findings, and returns a plan for your approval if the task implies changes
- `/summary [optional: file/area]` - summarize the whole project (good for onboarding or coming back to it)
- `/custom [optional: command]` - list every custom command and the daily workflow (in-session cheat sheet)

**Expert review lenses** (each gives a prioritized list, then offers to fix it in a plan)
- `/security [optional: file/area]` - security audit (OWASP-style: injection, auth, secrets, unsafe input)
- `/ux [optional: file/area]` - UX + accessibility review (flow, states, WCAG)
- `/law [optional: file/area]` - legal/compliance red flags (licenses, personal data, privacy) - flags only, not legal advice
- `/performance [optional: file/area]` - slow/unscalable code
- `/declutter [optional: file/area]` - over-complex code that could be simpler

> Review commands run on whatever model your session is using (no model is hardcoded). For a serious security or performance pass, switch your session to the strongest model you have first, then run the command.

---

## Manual vs auto = commands vs skills (the rule)
Your commands are **manual on purpose**: a command is manual (you type it to run it), a skill is auto (it fires itself when relevant). Keep anything with a consequence (actions, reviews) on manual commands; reserve skills (auto) for passive context that should surface on its own (e.g. "assume HIPAA rules when touching billing code"). If you keep re-explaining the same context, make *that* a skill. (Full reasoning in [`4-memory-and-commands-explained.md`](./memory-and-commands-explained.md).)

---

## Git, in plain language (no command needed)
- "What branches do I have?"
- "Switch to [branch]"
- "Create a new branch called [name] and switch to it"

`/commit` always commits to whichever branch is currently active.

---

## The daily loop (one line)
Open session (CLAUDE.md + DYNAMIC.md auto-brief Claude) -> Plan mode for real tasks -> approve -> auto-accept edits -> aim your context, use your commands -> review, never accept what you don't understand -> `/save` then `/commit` -> next session picks up from DYNAMIC.md.

---

## The one rule that matters
**Never accept what you don't understand.** If a change is unclear, `/explain` it or don't accept it.
