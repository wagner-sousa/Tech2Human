# AGENTS.md

## What this repo is

Tech2Human (T2H) is a **prompt-only Claude Code plugin** — no source code, no build, and no tests. The plugin consists of:

- `skills/tech2human/SKILL.md` — the skill prompt (input parsing, output rules, examples). This is the product.
- `.claude-plugin/plugin.json` — plugin manifest (name, version, metadata).
- `.claude-plugin/marketplace.json` — GitHub marketplace catalog used to distribute the plugin.

Everything else is documentation (`README.md`, `LICENSE`).

## Key constraints

- **No build/lint/test commands exist.** There is nothing to compile or run.
- **`skills/tech2human/SKILL.md` is the single source of truth** for plugin behavior. All translation rules, output modes (`--full`, `--response`), hard rules, safety constraints, and examples live there. Changes to plugin behavior = changes to this file.
- **`plugin.json` and `marketplace.json` must stay in `.claude-plugin/`** — this path is required by the Claude Code plugin system.
- **Version** lives in `plugin.json` (`"version": "1.1.0"`). Update it there when making releases.
- **`.kilo/` is gitignored** and must never be committed. It was untracked in commit `chore: remove .kilo/ from tracking`.

## Structure

```text
.claude-plugin/
├── plugin.json       # plugin manifest (name, version, metadata)
└── marketplace.json  # GitHub marketplace catalog
skills/
└── tech2human/
    └── SKILL.md      # skill prompt — all behavior lives here
README.md             # user-facing docs
LICENSE               # MIT
```

## Editing the skill

- The YAML front matter (`name`, `description`) is parsed by the plugin loader. Keep it valid YAML between the `---` fences.
- Output modes are controlled by flag combinations (`--full`, `--response`). The four mode matrix is documented in both `SKILL.md` and `README.md` — keep them in sync.
- Hard rules (section "Hard rules", 11 items) and safety rules are numbered. Preserve numbering when editing.
- Examples at the end of `SKILL.md` serve as few-shot prompts for the LLM. They are functional, not just documentation.
- Word limits: simple mode ≤ 80 words, full mode ≤ 200 words (rule 9).

## Invocation naming

- Primary and user-facing command: `/tech2human` (use this in instructions and examples).
- Canonical namespaced form: `/tech2human:tech2human` (useful when multiple plugins are loaded or a shortcut is ambiguous).
- The `name: tech2human` in `skills/tech2human/SKILL.md` frontmatter **must stay** — removing it can cause cached installs to fall back to an unstable install-directory name.

## Publishing and updates

- Validate before publishing: `claude plugin validate .`.
- The repository is both the plugin and its GitHub marketplace. The marketplace entry uses `"source": "./"` to install the plugin from the repository root.
- For a release, update `plugin.json` version, commit and push the changes, then tell users to run `/plugin marketplace update tech2human`.
- Community marketplace submissions use `https://platform.claude.com/plugins/submit`.

## Conventions

- The plugin responds in the **user's language**, not the error's language.
- `--response` mode must hide internal details (hostnames, IPs, file paths) — this is a hard security requirement (rule 11).
- No emojis in output (rule 8).
