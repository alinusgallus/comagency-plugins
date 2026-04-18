# LinkedIn Writer Plugin

Conversational LinkedIn post generator for Claude Code. A resource-first setup agent establishes your brand and reusable post-type presets from materials you already have. A writer agent then produces on-brand posts from a short brief.

This plugin is a standalone chat alternative to the ComAgency web form. It writes no data to the ComAgency database — everything lives in local `SKILL.md` files inside this directory.

## What's inside

```
skills/linkedin-writer/
├── .claude-plugin/plugin.json
├── agents/
│   ├── setup.md    # Brand setup + post-type creation
│   └── writer.md   # Generates posts from a brief
├── skills/         # Populated by the setup agent on first run
│   ├── brand-context/
│   ├── tone-format-guardrails/
│   └── post-types/
└── README.md
```

The `skills/` subdirectory starts empty. The setup agent writes into it based on your inputs.

## Install

From the ComAgency repo root:

```bash
claude --plugin-dir ./skills/linkedin-writer
```

Run `/reload-plugins` inside Claude Code after editing any plugin file.

## First-run flow

### 1. Brand setup

Invoke the setup agent. On the first run it detects that no brand context exists and starts the brand-setup phase.

It will ask if you have any existing materials — tone-of-voice docs, brand guidelines, past posts, a website URL, a LinkedIn profile. Paste text, drop file paths, or share URLs. It reads them, summarises what it inferred, and asks narrow follow-up questions only for gaps.

The output is two files:

- `skills/brand-context/SKILL.md` — company, audience, language, content pillars
- `skills/tone-format-guardrails/SKILL.md` — tone, format rules, guardrails, priority hierarchy

### 2. Create your first post type

Invoke the setup agent again. It now detects that brand context exists and switches to post-type setup.

Paste 1–3 example posts of the type you want to define (or link them, or point to a file). The agent proposes a pillar, framework (AIDPA / BAB / SPRC / PACSO), name, and description — you confirm or override.

The output is one file:

- `skills/post-types/<slug>/SKILL.md`

Repeat for each post type you want. Common starters: strategic analysis (SPRC), case study (BAB), trend alert (PACSO), announcement (AIDPA).

### 3. Write a post

Invoke the writer agent. Give it a one- or two-sentence brief. It picks a matching post type (or asks you to choose), fills gaps with targeted questions, generates the post, and self-checks against your guardrails.

## Everyday flow

- Most days: `writer` with a brief.
- New type of post (rare): `setup` to create a new preset first, then `writer`.
- Brand evolves: delete or edit `brand-context/SKILL.md` and `tone-format-guardrails/SKILL.md` and re-run `setup`.

## Relationship to ComAgency

This plugin mirrors the prompt-assembly logic of ComAgency's content generator (`flask_app/agents/content_generator.py`) — same priority hierarchy (Tone > Format > Guardrails > Brand), same framework list (from `flask_app/config/post-frameworks.json`), same structural split between brand-level and type-level inputs.

It does not read from the ComAgency database and does not push posts back to it. If you want a draft to end up in the ComAgency pipeline, copy the output into the web form — or use the existing `comagency-content-ideation` skill, which pushes ideas via MCP.

## Notes

- The plugin is brand-agnostic. The setup agent asks about your brand rather than hardcoding Colette Consulting. You can use it for any company.
- "Post type" is a plugin-only concept. ComAgency has no equivalent entity; the closest approximation there is a (pillar, framework) pair.
- All files are plain markdown. Edit them directly if you want to tweak behaviour without rerunning setup.
