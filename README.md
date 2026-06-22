# ghl-assist

A [Claude Code](https://claude.com/claude-code) skill that turns Claude into a **GoHighLevel (GHL) API expert** — endpoint documentation, request/response schemas, authentication guidance, rate limits, and hard-won gotchas from real-world integration work.

Use it when you're:

- Writing code that calls GHL endpoints
- Building workflows or automations that integrate with GHL
- Trying to understand GHL API capabilities, scopes, or data structures
- Debugging mysterious GHL API failures

## What's inside

- **API quick reference** — base URL, version headers, auth header, rate limits
- **Known gotchas** — Cloudflare User-Agent blocking, the dead legacy email-templates endpoint, GHL's `monetory` typo, agency-only vs. location-scope endpoints, and a read-only trick for recovering form schemas from the public widget payload
- **A two-layer documentation system** for organizing your own GHL knowledge base (official OpenAPI specs + your enriched notes)
- **OpenAPI spec reading guide** for the official GHL spec files
- **A self-improvement mandate** so the docs get more accurate every time an agent uses them

## Installing

Drop the skill folder into your Claude Code skills directory:

```bash
git clone https://github.com/<your-org>/ghl-assist.git ~/.claude/skills/ghl-assist
```

Claude Code will pick it up automatically. The skill activates whenever you ask about the GHL/GoHighLevel API.

## The knowledge base (optional but recommended)

The skill is most powerful when paired with a local knowledge base of GHL docs. Throughout `SKILL.md`, `<knowledge-base>/ghl/` refers to a directory **you** maintain:

```
<knowledge-base>/ghl/
├── index.md                          # your master index of endpoints + status
├── highlevel-api-docs/               # official specs, cloned from GHL's GitHub
│   └── apps/*.json                   #   OpenAPI 3.0 specs, one per API domain
└── api/*.md                          # your enriched, human-readable endpoint docs
```

To seed Layer 1 (the official specs):

```bash
cd <knowledge-base>/ghl
git clone https://github.com/GoHighLevel/highlevel-api-docs
```

Then build out Layer 2 (`api/*.md`) over time with usage notes, tested examples, and verified schemas. The skill's "Self-Improvement Mandate" encourages agents to keep these docs accurate as they go.

You don't *need* a pre-built knowledge base to get value — the gotchas and quick reference stand on their own — but the two-layer system is where the skill really pays off.

## Not affiliated with GoHighLevel

This is an independent, community-maintained skill. "GoHighLevel" and "GHL" are trademarks of their respective owner. Always cross-check against the [official GHL API docs](https://marketplace.gohighlevel.com/docs/).

## License

[MIT](./LICENSE)
