# LinkedIn Writer Plugin

Conversational LinkedIn post generator for Claude Code. A resource-first `setup` agent establishes your brand and reusable post-type presets from materials you already have. The `linkedin-writer` agent then produces on-brand posts from a short brief.

Fully self-contained — everything lives in local `SKILL.md` files inside this plugin directory. No external dependencies, no database, no API keys.

## What's inside

```
linkedin-writer/
├── .claude-plugin/plugin.json
├── agents/
│   ├── setup.md            # Brand setup + post-type creation
│   └── linkedin-writer.md  # Generates posts from a brief
├── skills/         # Populated by the setup agent on first run
│   ├── brand-context/
│   ├── tone-format-guardrails/
│   └── post-types/
└── README.md
```

The `skills/` subdirectory starts empty. The setup agent writes into it based on your inputs.

## Install

Install via the marketplace (recommended):

```
/plugin marketplace add <owner>/comagency-plugins
/plugin install linkedin-writer@comagency-plugins
```

Or mount the plugin directory directly for local development:

```bash
claude --plugin-dir /path/to/linkedin-writer
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

Invoke the `linkedin-writer` agent. Give it a one- or two-sentence brief. It picks a matching post type (or asks you to choose), fills gaps with targeted questions, generates the post, and self-checks against your guardrails.

## Everyday flow

- Most days: `linkedin-writer` with a brief.
- New type of post (rare): `setup` to create a new preset first, then `linkedin-writer`.
- Tweak an existing type (add examples, switch framework, rename): run `setup` and tell it you want to edit an existing type — it will preserve the example corpus.
- Brand evolves: delete or edit `brand-context/SKILL.md` and `tone-format-guardrails/SKILL.md` and re-run `setup`.

## Concepts

**Priority hierarchy.** When rules conflict, the writer resolves them in this order: **Tone > Format > Guardrails > Brand**. Voice and personality always win; brand context is background information and never overrides tone.

**Post type.** A reusable preset bundling a content pillar + a writing framework + 1–3 example posts + optional tone tweaks. Post types are plugin-local — you create whichever ones make sense for your brand.

**Frameworks.** Four built-in writing frameworks are offered during post-type setup:
- `AIDPA` — Attention / Interest / Desire / Proof / Action. For announcements, launches, conversion.
- `BAB` — Before / After / Bridge. For transformation stories, case studies.
- `SPRC` — Situation / Problem / Resolution / Conclusion. For analysis, thought leadership.
- `PACSO` — Problem / Aggravate / Consequence / Solution / Outcome. For pain-point posts, alerts.

## Notes

- The plugin is brand-agnostic. The setup agent asks about your brand rather than hardcoding anything. You can use it for any company.
- All files are plain markdown. Edit them directly if you want to tweak behaviour without rerunning setup.
- Delete a `skills/post-types/<slug>/SKILL.md` to retire a post type. Run setup again to add a new one.
