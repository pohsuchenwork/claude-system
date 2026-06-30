# Claude Code: Execution Plan

## The frame (read this first)
Claude Code isn't just a faster way to write code. It's a system for getting work done through three things: the context you give it, the plans you approve before it acts, and the loop of building and reviewing. Treat it like a chat box and you get mediocre results. Treat it like a workspace you set up and control, rules loaded, project state tracked, work planned before it's built, and you get real leverage.

The core idea: **your job isn't to keep re-explaining your project. It's to clearly say what you want, and let the setup carry the rest.** You stay in charge of deciding what "good" looks like, instead of babysitting every move.

---

## One-time, per new project
Run your setup wizard in Plan mode (the setting where Claude only proposes a plan and changes nothing until you approve). Review it, change what you don't like, approve. You now have CLAUDE.md (your rules), DYNAMIC.md (your current state), your commands, and a Git repo (the wizard runs `git init`, writes a `.gitignore` that keeps secrets and build output out of version control, and makes an initial commit as a clean rollback point). It then runs `/reload-skills` so the commands work right away (if they don't appear, restart the session) and prints a short workflow intro so you know what to do. Don't repeat this for the same project.

> **Verify the first time:** type `/save`, `/diffs`, `/explain something`, and `/custom` and confirm they work. Check `/save` keeps DYNAMIC.md short instead of letting it grow, `/diffs` compares against your last commit, and `/custom` reprints your commands and the workflow. These behaviors are what the system rests on.

---

## Every work session, the loop

### 1. Open the session
Start a new session. Claude auto-reads CLAUDE.md and DYNAMIC.md, so it already knows your stack, conventions, and where you left off. Don't re-explain the project, just state the task.

### 2. Plan the task first (for anything non-trivial)
- Turn on **Plan mode** (Claude proposes an approach, touches no files). Toggle it with **Shift+Tab** (cycle through the modes until the footer shows "plan mode on").
- Tell it what you want. It lays out *how* it'll do it, without writing code.
- Read the plan. Correct any misunderstanding. Approve only when it's right.
- **Dual-Claude review (for important or risky work):** open a *separate, fresh* session, paste in the plan, and ask it to find flaws and missing cases. A fresh session has no attachment to the plan, so it catches what the first one missed. Fix the plan, then go.
- Skip planning only for tiny tasks (typo, rename).

### 3. Let it build
- Approve the plan, then turn on **Auto-accept edits** (Claude makes its edits without asking you to approve each file, so it works smoothly).
- It still pauses before risky terminal actions (deleting, installing, running scripts), approve those deliberately.
- **Watch for Plan Mode silently exiting:** on a long session, Claude can switch *itself* out of Plan mode, going from "only planning" to "allowed to change files." If you've been relying on Plan mode for a while, glance at the footer to confirm it's still on, and if you see unexpected edits, re-activate it with Shift+Tab.
- Never use **Bypass mode** (removes all safety checks) outside a throwaway sandbox.

### 4. Give it the right context
What you show it determines what you get. Ways to give it what it needs:
- **@mention specific files** so it reads those directly instead of guessing.
- **Drag in files and documents** (PDFs, Word docs, data files, design files), it can read them.
- **Drag in screenshots** for a visual bug or a design to match, faster than describing it.
- **Give it the relevant files, not your whole project.** Point it at the few files that relate to your task. Dump everything and it has to dig through hundreds of files to find what matters, slower, less accurate, and it clogs its limited memory.
- **For big exploration**, use `/subagent` (see the command list) so the heavy reading happens off to the side.

### 5. Use your commands
Every command takes an optional argument to scope it (shown in brackets); run it bare for the default.
- `/explain [thing]` - explain code, a concept, or anything; attach a file or image and it explains that too.
- `/diffs [optional: file/area]` - show what changed since your last commit, explained.
- `/subagent [task]` - hand a task (usually big exploration across many files) to a *subagent*: a separate temporary Claude that works in its own memory and reports back a summary, keeping your main session clean. It decides if a subagent helps, picks a suitable model, uses one by default, reports back findings, and makes no changes on its own, if the task implies changes, it returns a plan for you to approve first.
- `/security`, `/ux`, `/law`, `/performance`, `/declutter` - run the review you want; add a file or area after it to scope the review. Each gives a prioritized list (worst first), then offers to fix it in a plan.
- `/summary [optional: file/area]` - get a plain-language overview of the whole project (handy for onboarding or coming back after time away).
- `/custom [optional: command]` - reprint all your commands and the daily workflow (in-session cheat sheet).
- These run on whatever model your session is set to. For a serious security or performance pass, switch to your strongest model first.

### 6. Check the work before accepting
Don't just trust it. Check in layers:
- **Live preview** - the app can actually *run your project* and show it working in front of you. First check: does it even run?
- **Read the changes (or `/diffs`)** - exactly what changed, old vs new. Read it for anything important.
- **Review-code button (or `/security`)** on bigger changes - asks Claude to double-check its own work. A free second look.
- **Test it yourself** - confirm it does what you wanted, not just that it runs.
- **If you don't understand a change, `/explain` it or don't accept it.** That's the line between using the tool and blindly trusting it.

### 7. If it goes wrong
- Comment on the specific changed line ("this part's wrong, do X") rather than re-explaining everything.
- If the whole approach is off, **start a fresh session** instead of arguing with a confused one. DYNAMIC.md holds your state, so starting over costs almost nothing.
- Roll back through git if a change needs undoing.

### 8. Save your progress (at every stopping point)
- **`/save`** - updates DYNAMIC.md with where things stand. Do it when you finish something, decide something, or stop.
- **`/commit [optional message]`** - reviews the branch, then commits with the message you typed, or one it writes for you if you typed nothing. You confirm, it saves.
- (Need a different branch first? Just say "switch to [branch]".)

---

## Manual vs auto = commands vs skills (what your tools are for)
Your commands are **manual on purpose** - *you* decide when each runs, which is what you want for anything with a consequence (a commit) or where timing is your call (a review, a save). The same idea is the **command vs skill** split: a command is manual (you type it), a skill is auto (it fires itself when its description matches what you're doing). Keep actions and reviews on manual commands; use a skill only for passive context that should surface on its own ("assume HIPAA rules when touching billing code"). Rule of thumb: if you keep re-explaining the same context, make *that* a skill. (Full reasoning in [`4-memory-and-commands-explained.md`](./memory-and-commands-explained.md).)

---

## Quick reference - when to do what

| Situation | Do this |
|---|---|
| Starting any real task | Plan mode first |
| Important or risky task | Plan mode + Dual-Claude review |
| Plan looks right | Auto-accept, let it build |
| Long session on Plan mode | Check Plan mode is still on |
| Big exploration across files | `/subagent [task]` (it picks a suitable model) |
| Don't understand a change | `/explain` it before accepting |
| Want a review | Run that command |
| Forgot the commands or the flow | `/custom` |
| Finished, or stopping | `/save` then `/commit` |
| Session got confused | Fresh session (DYNAMIC.md remembers) |
| Need to run tests | Run your test command locally (CI on a PR if you've set it up) |
| New, unrelated task | New session |
| Re-explaining the same context | Make it a skill (auto) |

---

## Hard rules
- Never accept a change you don't understand.
- Never use Bypass mode outside a sandbox.
- One session per task.
- `/save` at every stopping point (manual, so remember it).
- Keep CLAUDE.md short; add a command only when you catch real repetition.
- Manual commands for actions and reviews; skills (auto) only for passive context.

---

## The point, one more time
Every rule exists so you stay in the one role that matters: **clearly saying what you want, not managing the machine's every move.** Load your context, plan the work, check what matters, save your progress, repeat. The setup carries the rest. That's the difference between a chat box and a workspace.
