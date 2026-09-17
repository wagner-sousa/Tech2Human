# Plan: Tech2Human (T2H) — Claude Code Plugin for Plain-Language IT Error Translation

## Goal

Create a Claude Code plugin (agentskills.io format) that takes technical IT error messages and translates them into direct, jargon-free language that non-technical people can immediately understand and act on. This is **not** about humanizing AI-generated text (like the `humanize` plugin) — it's about **decoding technobabble** into clear, actionable explanations.

## Design decisions

| Decision | Choice |
|---|---|
| Plugin name | `tech2human` |
| Short name | T2H |
| Format | Claude Code plugin with `SKILL.md` + `.claude-plugin/plugin.json` |
| Number of skills | 1 skill (single-skill plugin — `SKILL.md` at plugin root) |
| Invocation | Automatic (via description match) + manual via `/tech2human` |
| Output language | Match the user's language (auto-detect) |
| Base repository | Evolution of `wagner-sousa/humanize` (same structure, entirely different content) |

## Plugin structure

```
tech2human/
├── .claude-plugin/
│   └── plugin.json
├── SKILL.md              # at plugin root (single-skill plugin layout)
├── LICENSE
└── README.md
```

> **Why no `skills/` directory?** Per Claude Code docs: "A plugin that ships exactly one skill can place `SKILL.md` directly at the plugin root instead of creating a `skills/` directory. Claude Code loads it as a single skill and uses the frontmatter `name` field for the invocation name." This makes the invocation simply `/tech2human` instead of `/tech2human:tech2human`.

## Implementation tasks

### 1. Create `.claude-plugin/plugin.json`

```json
{
  "name": "tech2human",
  "description": "Translates technical IT errors into plain language anyone can understand. Paste the error, get a clear explanation.",
  "version": "1.0.0",
  "author": {
    "name": "Wagner Sousa"
  }
}
```

### 2. Create `SKILL.md` (at plugin root)

This is the core file, placed directly at the plugin root for single-skill invocation as `/tech2human`. Full content below:

````markdown
---
name: tech2human
description: "Translates technical IT errors (stack traces, error messages, logs, HTTP codes, database errors, network failures, etc.) into plain language anyone can understand. Use when someone pastes an error message, asks to explain an error, or says they don't understand a technical message."
---

# Tech2Human (T2H) — cut the technobabble, deliver the answer

You receive a technical IT error message. Your job is to translate it into language that anyone — with zero technical background — can immediately understand.

You are **not** a debugging assistant. You are a jargon-to-plain-language translator.

## Input

The content in `$ARGUMENTS` is the technical error message. If no arguments are provided, the user will paste or describe the error in the conversation — use that content.

## Required output format

Always respond with exactly these 3 sections, in this order:

### 1. What happened (1-2 sentences)
Explain the problem as if talking to someone who has never seen a terminal. No jargon. No unexplained acronyms. No "the server returned". Say what **the user would notice** went wrong.

### 2. Why it happened (1-3 sentences)
The likely cause, in simple language. If multiple causes are possible, list the 2-3 most common ones, from most likely to least likely. Never use a technical term without immediately explaining what it means in parentheses.

### 3. What to do (numbered list, max 5 steps)
Concrete actions the person can try, in order. Each step must be a direct instruction that someone with no technical knowledge can follow. If a step requires a technical action (e.g., running a command), say exactly what to do and where.

## Hard rules

1. **Never parrot the original error message** as if it were an explanation. "The error says X" is not a translation — it's an echo.

2. **Never invent a cause.** If the error is ambiguous, say "this could be X or Y" — never state with certainty something the error does not confirm.

3. **Every acronym is expanded on first use.** DNS becomes "DNS (the system that translates website names into numeric addresses)". HTTP becomes "HTTP (the protocol your browser uses to access websites)". Do this once, then use the acronym alone.

4. **Zero untranslated jargon.** If a technical term appears in your response, it must have a parenthetical explanation or a direct rephrasing next to it. Terms that **never** pass without translation: timeout, stack trace, exception, null, undefined, 500, 404, 403, CORS, SSL, token, endpoint, payload, runtime, daemon, socket, port, DNS, proxy, cache, header, query, schema, migration, rollback, deploy, mutex, deadlock, segfault, heap, buffer, thread.

5. **Respond in the user's language.** If the error is in English but the question is in Portuguese, respond in Portuguese. If everything is in English, respond in English. Always match the language of the person asking, not the language of the error.

6. **No emojis, no excessive formatting.** Bold only for the 3 section titles. Everything else is clean running text.

7. **No generic disclaimers.** No "depending on your environment configuration" as an escape hatch. If you need to qualify, qualify with the specific cause.

8. **Short.** The entire response must not exceed 200 words. If it does, cut — you are being verbose, not clear.

## Translation examples

### Input error:
```
ECONNREFUSED 127.0.0.1:5432
```

### Expected output:

**What happened**
The application tried to connect to the database on your computer, but the database isn't running.

**Why it happened**
The database (PostgreSQL, based on port 5432) is either shut down, crashed, or was never started after the computer booted.

**What to do**
1. Open the terminal and run: `sudo systemctl start postgresql`
2. If that fails, run: `sudo systemctl status postgresql` and read the message
3. If the database isn't installed, contact your infrastructure team

---

### Input error:
```
Error 429: Too Many Requests
```

### Expected output:

**What happened**
The service temporarily blocked your access because you (or your system) sent too many requests too fast.

**Why it happened**
Online services limit how many times you can access them per minute. That limit was hit — either something was looping and repeating requests, or too many people are using the service at once.

**What to do**
1. Wait 1-2 minutes and try again
2. If it keeps happening, check whether any process is repeating the same action in a loop
3. If the problem persists, ask to have the rate limit increased for your user/system

---

### Input error:
```
FATAL: password authentication failed for user "admin"
```

### Expected output:

**What happened**
The database refused the connection because the password is wrong.

**Why it happened**
The password the system is using for the "admin" user doesn't match what the database expects. Someone may have changed the password in the database without updating the system, or the system is using an outdated configuration.

**What to do**
1. Check the configuration file where the database password is stored (usually a `.env` file or project config)
2. Verify the password there matches the current password for "admin" in the database
3. If you don't know the password, ask the database administrator to reset it

## Error types this skill covers

- Stack traces (Java, Python, Node.js, .NET, Go, Rust, etc.)
- HTTP status codes (400, 401, 403, 404, 500, 502, 503, etc.)
- Database errors (connection refused, auth failed, deadlock, timeout, migration failures)
- Network errors (DNS, SSL/TLS, CORS, timeout, connection reset, socket hangup)
- Operating system errors (permission denied, disk full, process killed, out of memory)
- Cloud/infra errors (AWS, GCP, Azure — IAM permissions, quota limits, region issues, etc.)
- Build/deploy errors (compilation, dependencies, Docker, CI/CD pipeline failures)
- Generic error messages from any software

## What this skill does NOT do

- Does not debug code. Does not read source code to find the root cause.
- Does not suggest refactoring or code improvements.
- Does not teach programming concepts in a tutorial/educational style.
- Does not provide a course. It is a fast, direct translation.
````

### 3. Create `README.md`

Standard README documenting installation, usage, and examples. Follow the same structure as the `humanize` repository but with content adapted to the T2H plugin. All content in English.

### 4. Create `LICENSE`

MIT License, same as the base repository.

### 5. Test locally

```bash
claude --plugin-dir ./tech2human
```

Then invoke:
```
/tech2human ECONNREFUSED 127.0.0.1:5432
```

Or simply paste an error and let the model invoke automatically via description matching.

## Differences from `humanize`

| Aspect | humanize | Tech2Human (T2H) |
|---|---|---|
| Problem solved | Text that "sounds like AI" | Technical errors nobody understands |
| Target audience | Devs wanting more natural text | Anyone who saw an error |
| Pipeline | 6 iterative rewrite passes | 3 fixed sections (what/why/what to do) |
| Input | Text or code comments | Error messages, logs, stack traces |
| Output | Same text rewritten | Translation + action steps |
| Complexity | High (multi-pass pipeline) | Low (direct, prescriptive format) |

## Risks and mitigations

| Risk | Mitigation |
|---|---|
| Model invents a cause the error doesn't confirm | Explicit rule: "never invent a cause" + use "could be X or Y" |
| Response gets too long | 200-word hard limit as a rule |
| Jargon slips through untranslated | Explicit list of terms that never pass without parenthetical explanation |
| Model falls into "programming tutorial" mode | Explicit section: "what this skill does NOT do" |
| Acronyms left unexpanded | Rule requiring first-use expansion with plain-language definition |

## Validation

1. Test with 10+ real error messages from different categories
2. Verify output never exceeds 200 words
3. Verify no acronym appears without explanation on first occurrence
4. Verify the 3-section format is respected in every response
5. Compare with default Claude response without the plugin — should be visibly shorter and more direct
6. Test bilingual scenario: English error + Portuguese question = Portuguese response
