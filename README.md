# Tech2Human

Translate technical errors into plain language.

Tech2Human (T2H) is a Claude Code plugin that cuts through IT technobabble and explains what went wrong, why it likely happened, and what to do next. It is designed for people who need a clear answer without learning the technical vocabulary first.

## What it does

Tech2Human handles application errors, stack traces, HTTP status codes, database failures, network errors, operating system messages, cloud incidents, build failures, and deployment errors.

Every response follows the same compact structure:

1. **What happened**: the visible problem in plain language.
2. **Why it happened**: the likely cause, including uncertainty when needed.
3. **What to do**: up to five concrete next steps.

The skill preserves commands, paths, error strings, and values needed to take action while explaining technical terms instead of repeating them.

## Installation

### Local development (load from folder)

1. Open the `/plugins` menu in Claude Code.
2. Select "Install from folder…" and choose the `tech2human/` directory.

### Marketplace (when published)

```bash
claude plugins install <marketplace-name>
```

### Test with a local clone (advanced)

Run Claude Code with the plugin directory loaded for the current session:

```bash
claude --plugin-dir ./tech2human
```

## Usage

Invoke the skill directly with:

```text
/tech2human ECONNREFUSED 127.0.0.1:5432
```

You can also paste an error in a conversation and let Claude Code invoke the skill automatically when the request matches its description.

The response language follows the user's language. For example, an English error with a Portuguese question receives a Portuguese explanation.

## Example

Input:

```text
Error 429: Too Many Requests
```

Output:

### What happened

The service temporarily blocked access because it received too many requests too quickly.

### Why it happened

Online services limit how many requests a person or system can make in a period of time. That limit was reached, possibly because something is repeating the same action or because the service is unusually busy.

### What to do

1. Wait a few minutes and try again.
2. If it happens repeatedly, check for a process or script that is sending the same request in a loop.
3. If the service is yours, ask its provider about the request limit and how to increase it.

## Design principles

- No invented causes.
- No unexplained jargon or acronyms.
- No generic disclaimers.
- No unnecessary tutorials or debugging detours.
- Short, direct, actionable responses.
- Security and destructive-action warnings remain explicit.

## Project structure

```text
tech2human/
├── .claude-plugin/
│   └── plugin.json
├── SKILL.md
├── LICENSE
└── README.md
```

This is a single-skill plugin, so `SKILL.md` is located at the plugin root and the command is `/tech2human`.

## License

MIT. See [LICENSE](LICENSE).