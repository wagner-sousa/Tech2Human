# Tech2Human

Translate technical IT errors into plain language for client-facing responses, support tickets, and non-technical documentation.

Tech2Human (T2H) is a Claude Code plugin that cuts through IT technobabble and explains what went wrong in language suitable for non-technical audiences. It is designed for support teams who need to communicate technical issues to clients or stakeholders without the technical vocabulary.

## What it does

Tech2Human handles application errors, stack traces, HTTP status codes, database failures, network errors, operating system messages, cloud incidents, build failures, and deployment errors.

It provides **four output modes** controlled by flags at the start of the input:

| Flags | Format | Tone | Use case |
|-------|--------|------|----------|
| *(none)* | Single paragraph | Neutral translation | Internal notes, agent adaptation |
| `--response` | Single paragraph | Third-person impersonal, client-safe | Ticket reply, email to client, status page |
| `--full` | Three sections (adapted to user's language) | Neutral translation | Internal runbooks, detailed documentation |
| `--response --full` | Three sections | Third-person impersonal, client-safe | Formal incident report for client |

### Client-safe by default in `--response` mode

When using `--response`, the output automatically hides internal details such as server hostnames, IP addresses, file paths, and implementation specifics — replacing them with generic references like "the service" or "the system" (adapted to the user's language).

The response language follows the user's language. For example, an English error with a Portuguese question receives a Portuguese explanation.

## Installation

### Local development (load from folder)

1. Open the `/plugins` menu in Claude Code.
2. Select "Install from folder…" and choose the `Tech2Human/` directory.

### Marketplace (when published)

```bash
claude plugins install <marketplace-name>
```

### Test with a local clone (advanced)

Run Claude Code with the plugin directory loaded for the current session:

```bash
claude --plugin-dir ./Tech2Human
```

## Usage

Invoke the skill directly with:

```text
/tech2human ECONNREFUSED 127.0.0.1:5432
```

Or use flags for different output modes:

```text
# Client-ready response (single paragraph)
/tech2human --response Estamos com problema de cache na AWS

# Detailed internal analysis (three sections)
/tech2human --full ECONNREFUSED 127.0.0.1:5432

# Client-ready detailed incident report
/tech2human --response --full Error 503: Service Unavailable
```

You can also paste an error in a conversation and let Claude Code invoke the skill automatically when the request matches its description.

Flags must appear **before** the message body. Multi-line input (stack traces, logs) is supported:

```text
/tech2human --response
java.lang.NullPointerException
    at com.example.MyService.process(MyService.java:42)
```

## Example

### Client-facing response (`--response`)

Input:

```text
--response Estamos com problema de cache na AWS
```

Output:

Foi identificado um problema no sistema de memória temporária que pode causar lentidão no carregamento de informações. A situação está sendo analisada. Caso persistam dificuldades, a equipe técnica poderá fornecer mais detalhes.

## Design principles

- No invented causes.
- No unexplained jargon or acronyms.
- No generic disclaimers.
- No unnecessary tutorials or debugging detours.
- Short, direct, actionable responses.
- Security and destructive-action warnings remain explicit.
- **Client-safe by default in `--response` mode** — internal details are never exposed.

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