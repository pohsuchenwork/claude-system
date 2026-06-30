# Claude Code: Memory & Commands Explained

*A clear guide to what goes in CLAUDE.md, what goes in DYNAMIC.md, and what becomes a command, with the reasoning behind each.*

---

## The big picture: three places knowledge lives

Claude forgets everything between sessions. It reconstructs its understanding by *reading* things at the start of each session. So "memory" is really just deciding what to write down, and where. There are three homes, and the whole skill is putting each fact in the right one.

| File | Holds | Changes? | Think of it as |
|---|---|---|---|
| **CLAUDE.md** | Stable rules: stack, structure, conventions, commands, guardrails | Rarely | The permanent house rules |
| **DYNAMIC.md** | Current state: what's in progress, recent decisions, active gotchas | Constantly | A handoff note to the next session |
| **Git history** | Everything that ever happened, every change | Immutable history | The permanent record |

One-line version: **CLAUDE.md = how it works. DYNAMIC.md = where it stands. Git = what happened.**

> **Important reality check:** CLAUDE.md and DYNAMIC.md are *advice Claude reads*, not rules it's forced to obey - in practice an instruction in them is followed most of the time, but not reliably every time, so they're guidance, not guarantees. For something that must happen every time without fail (like running a check before every commit), a written rule isn't enough; that needs a hook (an automatic script). Keep that in mind: these files make the right behavior *much more likely*, not certain.

---

## CLAUDE.md, the permanent rules

**The test for every line:** Is this stable (true long-term), AND can Claude *not* figure it out by reading the code? If both, it belongs here. If not, it doesn't.

### Put in CLAUDE.md

1. **Tech stack and versions** - "Next.js 14, TypeScript, Tailwind." So Claude doesn't write for the wrong framework or an old version.
2. **How the project is organized** - "Components in /components, API routes in /app/api, utils in /lib." So new code goes in the right place.
3. **Conventions you want followed** - "Functional components, named exports, async/await over promises." The choices the code doesn't enforce but you want consistent.
4. **Hard rules / guardrails** - "Never edit /generated. Never commit secrets. Always write tests for new functions." (Note: these are strong suggestions, followed most of the time but not iron guarantees, see the reality check above.)
5. **Commands Claude should know** - "Build: npm run build. Test: npm test. Dev: npm run dev." *This is the single highest-value section*, without it, Claude guesses your commands and wastes turns on ones that were never going to work.
6. **Permanent project quirks** - "Always use the useAuth() hook, never call the auth library directly." Stable gotchas that are always true.
7. **A short "When compacting context, preserve:" note** - list what must survive auto-compaction (the current task, key decisions, active gotchas). When the window fills and the tool auto-summarizes, this is what tells it not to drop the things that matter.

### Keep out of CLAUDE.md

1. **Anything Claude can read in the code.** Don't paste code or list files. Claude reads the code itself, *and* it has auto memory that learns your patterns after one session. Writing down what it'll figure out anyway just wastes space and goes stale.
2. **Evolving / in-progress state.** "We're halfway through the migration." That's DYNAMIC.md's job.
3. **Fixed issues or history.** "We had a bug last week." Noise once resolved. (Git has it.)
4. **Padding and essays.** Long explanations dilute the rules that matter. Every extra line makes the important ones harder to find, and competes for Claude's limited attention.
5. **Generic programming advice.** "Write clean code. Think step by step." Claude already does this. It changes nothing and wastes space.
6. **Maybes.** If a rule isn't settled, leave it out until it is.

### How long should it be?
**Short. Aim for under ~200 lines, and under ~120 is better.** This isn't arbitrary: a model can only hold so many instructions in focus at once, and Claude Code's own system prompt already takes up part of that budget. Past a couple hundred lines, the rules that matter get diluted and adherence quietly drops. If yours is growing, you're probably putting in things that belong in the code, in DYNAMIC.md, or nowhere.

### The mental model
CLAUDE.md is the onboarding brief for a new hire: "here's our stack, here's where things go, here's how we do things, here's what never to touch." It is NOT a description of the code (Claude reads that), a status update (DYNAMIC.md), or a history log (Git).

### A good CLAUDE.md
```
# Project Rules

## Stack
Next.js 14, TypeScript, Tailwind CSS, Postgres via Prisma.

## Structure
- Pages/routes: /app
- Components: /components
- Shared logic: /lib
- Database schema: /prisma/schema.prisma

## Conventions
- Functional components only. Named exports.
- Use the useAuth() hook for anything auth-related, never call the auth library directly.
- All API responses use the wrapper in /lib/response.ts.

## Rules
- Never edit /generated (auto-built).
- Always write a test for new functions in /lib.
- Never commit .env files.

## Commands
- Dev: npm run dev
- Build: npm run build
- Test: npm test

## When compacting context, preserve
The current task, key decisions, and active gotchas.

## Also
At the start of every session, read DYNAMIC.md and treat it as the current source of truth for in-progress work, recent decisions, and anything learned about this project.
```
Short, all stable, all stuff Claude can't infer from the code, ending with the pointer to DYNAMIC.md. That's the whole job.

---

## DYNAMIC.md, the current state

**The test for every line:** Would a fresh Claude, starting tomorrow, do the wrong thing or waste time without knowing this? If yes, it goes in. If no, leave it out.

### Put in DYNAMIC.md

1. **What you're in the middle of** - "Migrating auth from sessions to JWT. Frontend done, backend routes still on old middleware." A new session needs this or it has no idea what's in progress.
2. **Decisions still in effect that aren't obvious from the code** - "Using JWT (not sessions) for scalability. Don't revert." So Claude doesn't undo a deliberate choice. *Current ones only*, not ones you've reversed.
3. **Active gotchas / landmines** - "Run npm run seed before tests or they fail on an empty DB." The stuff that isn't visible in the code but will bite.
4. **Where things stand** - a short status: what works, what's broken, what's next.
5. **Things Claude figured out worth keeping** - "The real build command is npm run build:dev, not the obvious one." Practical knowledge that saves rediscovery.

### Keep out of DYNAMIC.md

1. **Anything already in the code.** The code is the source of truth for what the code is.
2. **Stable rules.** Conventions, framework, style, that's CLAUDE.md's job. Don't mix them.
3. **Fixed/resolved issues.** Once it's fixed and the fix is in the code, it's history and noise. (Git has it.)
4. **A play-by-play changelog.** That's what Git is for. Don't rebuild it here.
5. **Anything no longer true.** The moment something changes, the old version comes *out*. This is a current snapshot, not an archive.

### The critical discipline: overwrite, don't append
This is what keeps DYNAMIC.md useful. Every update *rewrites* it to be currently true, replacing stale lines, removing what's done. It should stay roughly the same size every time, not grow. The second it becomes a growing log of everything that ever happened, it starts hurting instead of helping, it loads every session, so a bloated one fills Claude's memory with old noise before you've typed a word.

### The mental model
A handoff note to the next person, except the next person is a fresh Claude with amnesia about your conversation but full ability to read the code. So you don't tell them what they can read themselves (the code) or the permanent rules (CLAUDE.md). You tell them what *isn't* visible and *isn't* permanent: what you're doing now, what you decided, what'll bite them, where you left off.

### A good DYNAMIC.md
```
## Currently working on
Migrating auth from sessions to JWT. Frontend done. Backend routes
still use old session middleware, need converting next.

## Active decisions
- Using JWT (not sessions) for scalability. Don't revert.
- Database is now Postgres, not MySQL (switched last week).

## Gotchas
- Run `npm run seed` before tests or they fail on empty DB.
- The /payments route is fragile, test manually after any change.

## Status
Login works. Signup works. Password reset half-built (not functional yet).
```
Short, current, all stuff a fresh session needs and can't get from the code. Notice what's *not* there: no fixed bugs, no changelog, no style rules, no code explanations. Just the live state.

---

## Commands, saved prompts for repeated jobs

**The test for a command:** Is this a specific task you do *repeatedly* and want triggered the same way every time? Commands are for *actions you repeat*, not knowledge (that's the memory files).

### Make commands for

1. **Stuff you retype constantly.** The #1 signal: if you've typed roughly the same request three times, make it a command. (e.g. /security holds a whole security-review prompt.)
2. **Multi-step routines you want done consistently.** A sequence run the same way each time.
3. **Your "lens" passes.** /security, /ux, /law, /performance, /declutter, repeatable review modes you run on demand.
4. **Project maintenance actions.** /save (update DYNAMIC.md), /diffs (explain what changed), /custom (list every command and the daily workflow). Specific jobs on your project.
5. **Anything with a consistent output format.** Bake the format in so you don't re-specify it every time.

### Don't make commands for

1. **One-off requests.** If you'll ask it once, just ask it. The whole value is repetition.
2. **Stable project knowledge.** "We use React." That's CLAUDE.md. Commands *do things*; CLAUDE.md *informs*.
3. **Things that change every time.** A command holds the *constant* part; the variable part you type when you run it.
4. **Speculative commands.** Don't invent 20 for hypothetical tasks, you'll guess wrong and clutter the menu. Make one *when* you catch the repetition.
5. **Trivial three-word asks.** If typing the request is faster than remembering the command, skip it. Commands earn their place by saving a *paragraph*, not three words.

### The mental model
A command is a saved prompt for a job you do over and over, the difference between retyping "review this for security vulnerabilities, focus on injection and auth, explain each issue" every time, versus typing /security. The command holds the long, constant instruction; you just trigger it.

The rule that keeps your set clean: **don't build commands you predict you'll need. Build them the moment you catch yourself typing the same thing a third time.** That way every command is one you actually use, not a graveyard of hypotheticals.

### Commands vs skills (manual vs auto)
This is the same distinction under two names. A **command is manual** - you type it to run it, you control when. A **skill is auto** - it fires itself when its description matches what you're doing. So manual = command, auto = skill.

(As of 2026 the two are *unified*: a file at `.claude/commands/save.md` and a skill at `.claude/skills/save/SKILL.md` both create `/save`. Commands are the simpler form, one file, manually triggered. Skills live in a folder, can carry extra config, and can auto-activate. Both are fully supported, so this is a choice of form, not a question of one being deprecated.)

For deliberate review passes (/security, /ux, /law), **commands (manual) are the right call**, you want to *choose* when a security review runs, not have it fire unpredictably mid-task. Reserve skills (auto) for a different kind of thing: passive knowledge or conventions that should surface whenever relevant ("assume HIPAA rules when touching billing code"), where a slightly-off trigger costs nothing. Never auto-fire an *action*; those stay manual.

### A good command
`.claude/commands/security.md` (this is the actual `/security` the wizard builds, shortened here):
```
You are a senior application security engineer doing a security audit.
Think adversarially: for each piece of code, ask how an attacker would
exploit it. Review against OWASP Top 10 and common CWE categories for
injection, broken auth and access control, exposed secrets, insecure
data handling, and unsafe input. Produce a prioritized list of findings,
ordered by severity (Critical, High, Medium, Low), most serious first.
For each: the vulnerability, its severity, the attack scenario, and the
concrete fix. Don't pad with trivial issues. Then ask if I want them
fixed; if yes, switch to Plan mode and propose a plan before changing
anything.
```
You type /security, that whole prompt runs, the same way every time. That's the value: a paragraph of precise instruction, triggered with one word. (The persona and severity-ranking matter, "senior security engineer thinking adversarially" produces sharper findings than a bare "check for security issues.")

---

## The filter, all three at once

For any fact you're about to write down, ask:

- **Stable, and Claude can't read it from the code?** -> CLAUDE.md
- **Changes as you work (in-progress, current decisions, live gotchas)?** -> DYNAMIC.md
- **Already in the code?** -> leave it out (Claude reads it; auto memory learns it)
- **Fixed or old?** -> leave it out (Git has it)
- **A repeated *action* you trigger?** -> a command
- **Passive context that should auto-apply when relevant?** -> a skill

Everything in this system comes back to one discipline: **lean, current, high-signal.** Write down only what genuinely helps, keep it true, and let the code, Git, and Claude's own memory carry the rest.
