# Tech2Human

Tech2Human (T2H) translates technical IT errors into plain language for client-facing responses, support tickets, and non-technical documentation.

It is a Claude Code plugin for support teams that need to explain application errors, stack traces, HTTP status codes, database failures, network errors, operating-system messages, cloud incidents, build failures, and deployment problems without technical jargon.

## Installation

### GitHub marketplace

The repository is both the plugin and its GitHub marketplace. Add the marketplace and install the plugin:

```text
/plugin marketplace add wagner-sousa/Tech2Human
/plugin install tech2human@tech2human
```

CLI equivalent:

```bash
claude plugin marketplace add wagner-sousa/Tech2Human
claude plugin install tech2human@tech2human
```

The marketplace entry uses the repository root as its plugin source, so updates are delivered when changes are committed and pushed.

### Claude Code community marketplace

After the plugin is approved in Anthropic's community marketplace, users can install it with:

```text
/plugin marketplace add anthropics/claude-plugins-community
/plugin install tech2human@claude-community
```

### Local development

Load the repository directly for the current Claude Code session:

```bash
claude --plugin-dir .
```

For local marketplace testing, run this from the repository root:

```text
/plugin marketplace add .
/plugin install tech2human@tech2human
```

Run `/reload-plugins` after changing the plugin during development.

## Usage

Use the short command in instructions and everyday use:

```text
/tech2human ECONNREFUSED 127.0.0.1:5432
```

The fully namespaced form also works and is useful when multiple plugins are loaded or a shortcut is ambiguous:

```text
/tech2human:tech2human ECONNREFUSED 127.0.0.1:5432
```

Flags must appear before the technical message. Both flags can be combined in either order.

### Output modes

| Flags | Format | Tone | Use case |
| --- | --- | --- | --- |
| *(none)* | One paragraph | Neutral translation | Internal notes or support-agent adaptation |
| `--response` | One paragraph | Third-person, client-safe | Ticket reply, email, or status update |
| `--full` | Three sections | Neutral translation | Internal runbook or detailed documentation |
| `--response --full` | Three sections | Third-person, client-safe | Formal client-facing incident report |

Examples:

```text
# Client-ready response
/tech2human --response Estamos com problema de cache na AWS

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

## Updates

For each release:

1. Update `"version"` in `.claude-plugin/plugin.json`.
2. Commit and push the changes.
3. Ask users to refresh the marketplace:

```text
/plugin marketplace update tech2human
```

## Publication

Validate the repository before publishing:

```bash
claude plugin validate .
```

Then submit the plugin for community-marketplace review through the Claude Console:

```text
https://platform.claude.com/plugins/submit
```

The GitHub marketplace is available as soon as the repository containing `.claude-plugin/marketplace.json` is pushed. Community-marketplace availability depends on Anthropic's review and safety screening.

## Project structure

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

Only the manifests belong in `.claude-plugin/`. The skill is stored under `skills/tech2human/` so Claude Code discovers it as the `tech2human` skill in the `tech2human` plugin namespace.

## Official documentation

- [Create plugins](https://code.claude.com/docs/en/plugins)
- [Plugin marketplaces](https://code.claude.com/docs/en/plugin-marketplaces)
- [Plugins reference](https://code.claude.com/docs/en/plugins-reference)

## License

MIT. See [LICENSE](LICENSE).
