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

## The knowledge base

All documentation lives **inside the skill directory** — a self-contained, two-layer system:

```
ghl-assist/
├── index.md                          # master index: routing table + Known Gotchas
├── layer-2-enriched-docs/            # enriched human-readable docs (BUNDLED ✅)
│   ├── contacts/  invoices/  ...     #   large domains split into folders w/ README
│   └── oauth.md   workflows.md  ...  #   smaller domains as single files
└── layer-1-official-docs/            # official GHL OpenAPI specs (you clone these)
    └── apps/*.json                   #   OpenAPI 3.0 specs, one per API domain
```

**Layer 2 (enriched docs) ships with the skill** — 24 API domains documented with usage notes, gotchas, tested examples, and verified schemas. Start at `index.md` to find the right file for any GHL area.

**Layer 1 (official specs) is one quick clone.** From the skill directory:

```bash
cd ~/.claude/skills/ghl-assist
git clone https://github.com/GoHighLevel/highlevel-api-docs layer-1-official-docs
```

Keep them current with `git pull` inside `layer-1-official-docs/`. (The clone is git-ignored, so it won't conflict with the skill's own repo.)

The skill is fully usable the moment it's installed — Layer 1 just adds the complete raw OpenAPI specs as a fallback for endpoints the enriched docs haven't covered yet. The skill's "Self-Improvement Mandate" encourages agents to keep the enriched docs accurate as they go.

## Not affiliated with GoHighLevel

This is an independent, community-maintained skill. "GoHighLevel" and "GHL" are trademarks of their respective owner. Always cross-check against the [official GHL API docs](https://marketplace.gohighlevel.com/docs/).

## License

[MIT](./LICENSE)
