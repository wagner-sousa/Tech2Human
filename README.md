# Tech2Human

![Tech2Human cover](assets/cover.jpg)


> Translate technical IT errors into clear, human-friendly language.

## About

Tech2Human is a prompt-only Claude Code plugin for support teams and engineers who need to explain technical problems without unnecessary jargon.

It translates application errors, stack traces, HTTP status codes, database failures, network errors, operating-system messages, cloud incidents, build failures, and deployment problems into language that non-technical audiences can understand.

## Technologies

- Claude Code plugin system
- Markdown-based skill prompt
- Claude Code plugin and marketplace manifests

## Prerequisites

- [Claude Code](https://code.claude.com/docs/en/overview)
- Access to a Claude Code plugin marketplace or a local clone of this repository

## Installation

### GitHub marketplace

The repository is both the plugin and its marketplace. Add the marketplace and install the plugin from Claude Code:

```text
/plugin marketplace add wagner-sousa/Tech2Human
/plugin install tech2human@tech2human
```

The equivalent CLI commands are:

```bash
claude plugin marketplace add wagner-sousa/Tech2Human
claude plugin install tech2human@tech2human
```

### Claude Code community marketplace

After the plugin is approved in Anthropic's community marketplace, install it with:

```text
/plugin marketplace add anthropics/claude-plugins-community
/plugin install tech2human@claude-community
```

### Local development

Load the repository directly in the current Claude Code session:

```bash
claude --plugin-dir .
```

For local marketplace testing, run these commands from the repository root:

```text
/plugin marketplace add .
/plugin install tech2human@tech2human
```

Run `/reload-plugins` after changing the plugin during development.

## Usage

Use `/tech2human` followed by a technical message, error, log, or stack trace:

```text
/tech2human ECONNREFUSED 127.0.0.1:5432
```

Flags must appear before the technical message. Both flags can be combined in either order.

### Output modes

| Flags | Format | Tone | Use case |
| --- | --- | --- | --- |
| None | One paragraph | Neutral translation | Internal notes or support-agent adaptation |
| `--response` | One paragraph | Third-person, client-safe | Ticket reply, email, or status update |
| `--full` | Three sections | Neutral translation | Internal runbook or detailed documentation |
| `--response --full` | Three sections | Third-person, client-safe | Formal client-facing incident report |

Examples:

```text
# Client-ready response
/tech2human --response We are experiencing a cache issue in our cloud environment

# Detailed internal explanation
/tech2human --full ECONNREFUSED 127.0.0.1:5432

# Detailed client-facing incident report
/tech2human --response --full Error 503: Service Unavailable
```

Multi-line errors and stack traces are supported:

```text
/tech2human --response
java.lang.NullPointerException
    at com.example.MyService.process(MyService.java:42)
```

The response language follows the user's language. In `--response` mode, internal hostnames, IP addresses, file paths, and implementation details are replaced with generic references.

## Environment Variables

Tech2Human does not require environment variables, API keys, databases, or application services. Never add secrets to the repository when using or developing the plugin.

## Project Structure

```text
Tech2Human/
├── .claude-plugin/
│   ├── plugin.json
│   └── marketplace.json
├── skills/
│   └── tech2human/
│       └── SKILL.md
├── README.md
└── LICENSE
```

The manifests belong in `.claude-plugin/`. The skill is stored under `skills/tech2human/` so Claude Code can discover it as the `tech2human` skill.

## Tests and Validation

This is a prompt-only plugin, so it has no build, dependency installation, or automated test suite. Validate the plugin before publishing:

```bash
claude plugin validate .
```

## Updates

For each release:

1. Update `"version"` in `.claude-plugin/plugin.json`.
2. Commit and push the changes.
3. Ask users to refresh the marketplace:

```text
/plugin marketplace update tech2human
```

## Publication

Submit the plugin for community-marketplace review through the Claude Console:

```text
https://platform.claude.com/plugins/submit
```

The GitHub marketplace is available after the repository containing `.claude-plugin/marketplace.json` is pushed. Community-marketplace availability depends on Anthropic's review and safety screening.

## License

MIT. See [LICENSE](LICENSE).
