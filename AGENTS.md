# AGENTS.md

## What this repo is

Tech2Human (T2H) is a **prompt-only Claude Code plugin** — no source code, no build, no tests. The entire plugin is two files:

- `SKILL.md` — the skill prompt (input parsing, output rules, examples). This is the product.
- `.claude-plugin/plugin.json` — plugin manifest (name, version, metadata).

Everything else is documentation (`README.md`, `LICENSE`).

## Key constraints

- **No build/lint/test commands exist.** There is nothing to compile or run.
- **`SKILL.md` is the single source of truth** for plugin behavior. All translation rules, output modes (`--full`, `--response`), hard rules, safety constraints, and examples live there. Changes to plugin behavior = changes to `SKILL.md`.
- **`plugin.json` must stay in `.claude-plugin/`** — this path is required by the Claude Code plugin system.
- **Version** lives only in `plugin.json` (`"version": "1.1.0"`). Update it there when making releases.

## Structure

```
.claude-plugin/plugin.json   # plugin manifest (name, version, repo URL)
SKILL.md                     # skill prompt — all behavior lives here
README.md                    # user-facing docs
LICENSE                      # MIT
```

## Editing `SKILL.md`

- The YAML front matter (`name`, `description`) is parsed by the plugin loader. Keep it valid YAML between the `---` fences.
- Output modes are controlled by flag combinations (`--full`, `--response`). The four mode matrix is documented in both `SKILL.md` and `README.md` — keep them in sync.
- Hard rules (section "Hard rules", 11 items) and safety rules are numbered. Preserve numbering when editing.
- Examples at the end of `SKILL.md` serve as few-shot prompts for the LLM. They are functional, not just documentation.
- Word limits: simple mode ≤ 80 words, full mode ≤ 200 words (rule 9).

## Conventions

- The plugin responds in the **user's language**, not the error's language.
- `--response` mode must hide internal details (hostnames, IPs, file paths) — this is a hard security requirement (rule 11).
- No emojis in output (rule 8).
