# LinkedIn Writer

A Claude Code plugin for writing on-brand LinkedIn posts. Give it a one- or two-sentence brief, get back a post that matches your voice, format rules, and content pillars.

**Who it's for.** Founders, content marketers, and small agencies who already have a brand voice and want to stop fighting generic AI output. The plugin learns your brand from materials you already have — a tone-of-voice doc, past posts, your website — and reuses it every time.

**What makes it different.** No API keys, no database, no external services. Everything runs inside Claude Code or Claude Cowork, and state lives as plain markdown in the plugin's persistent data directory — surviving across sessions and plugin updates.

**Language.** The plugin is brand-agnostic. It works in French, English, or any other language your brand writes in — set once during setup.

**Where it runs.** Designed for [Claude Cowork](https://support.claude.com/en/articles/13837440-use-plugins-in-claude-cowork). Also works in local Claude Code (state goes to `~/.claude/plugins/data/linkedin-writer-comagency-plugins/`).

## Install

Inside Claude Code:

```
/plugin marketplace add alinusgallus/comagency-plugins
/plugin install linkedin-writer@comagency-plugins
```

## What using it feels like

**First run.** Invoke the `linkedin-writer` agent. It detects that no brand is set up yet and starts the setup flow in the same conversation:

> Before we write anything, do you have existing materials I can read? Tone-of-voice docs, brand guidelines, past posts, your website. Paste text, give me file paths, or share URLs.

You point it at a tone doc and your homepage. It reads both, summarises what it inferred — company, audience, 3 content pillars, tone rules, format preferences, quality guardrails — and asks you to confirm or correct. You adjust two things. It writes the brand to two markdown files inside the plugin.

It then asks for 1–3 example posts of the first *post type* you want to create. You paste three of your best "strategic analysis" posts. It proposes a name, a matching content pillar, a framework (one of AIDPA, BAB, SPRC, PACSO), and a one-line description of when to use this type. You confirm.

Setup done. It asks:

> What do you want to post about?

You type a brief. It picks the matching post type, asks for any angle or data you want to include, and generates the post. Plain text, respecting your word count, hashtag count, and guardrails, in your voice. It runs a quick self-check against the guardrails and prints the compliance verdict beside the post.

**Everyday use.** Invoke `linkedin-writer`, type a brief, iterate if you want revisions. That's the whole flow.

## Managing post types and brand later

- **Add a new post type:** invoke the `setup` agent. It detects that brand context exists and walks you through adding another preset.
- **Edit a post type** (add examples, change framework, rename): invoke `setup` and tell it you want to edit. It lists the existing types, you pick one, it preserves your examples while you change whatever else.
- **Tweak your brand voice:** edit `skills/brand-context/SKILL.md` or `skills/tone-format-guardrails/SKILL.md` directly with any text editor, or delete them and run `setup` again for a clean slate.
- **Retire a post type:** delete `skills/post-types/<slug>/SKILL.md`.

## Concepts

**Priority hierarchy.** When rules conflict, the writer resolves them in this order: **Tone > Format > Guardrails > Brand**. Voice always wins over structural rules; brand context is background and never overrides voice.

**Post type.** A reusable preset bundling a content pillar, a writing framework, 1–3 example posts, and optional tone tweaks. Create as many as you want.

**Built-in frameworks:**

| Framework | Shape | Good for |
|-----------|-------|----------|
| `AIDPA` | Attention / Interest / Desire / Proof / Action | Announcements, launches, conversion |
| `BAB` | Before / After / Bridge | Transformation stories, case studies |
| `SPRC` | Situation / Problem / Resolution / Conclusion | Analysis, thought leadership |
| `PACSO` | Problem / Aggravate / Consequence / Solution / Outcome | Pain-point posts, trend alerts |

## Under the hood

Two agents ship with the plugin. All user state — brand, tone rules, post types — is written to the plugin's persistent data directory (`${CLAUDE_PLUGIN_DATA}`), which survives sessions and plugin updates.

```
Plugin install directory (${CLAUDE_PLUGIN_ROOT}, read-only):
  linkedin-writer/
  └── agents/
      ├── setup.md            # Conversational setup: brand + post types
      └── linkedin-writer.md  # Writes posts; runs setup inline if missing

Persistent data directory (${CLAUDE_PLUGIN_DATA}, populated by setup):
  ├── brand-context/SKILL.md           # Company, audience, language, pillars
  ├── tone-format-guardrails/SKILL.md  # Tone, format, quality rules + priority
  └── post-types/<slug>/SKILL.md       # One file per reusable preset
```

On a local install, the data directory resolves to `~/.claude/plugins/data/linkedin-writer-comagency-plugins/`. In Claude Cowork, Anthropic manages the path. Either way, every file is plain markdown — inspect or edit by hand at any time.

## Troubleshooting

- **"No brand context yet" even though I ran setup:** check that `brand-context/SKILL.md` exists in the plugin's persistent data directory. On local Claude Code that's `~/.claude/plugins/data/linkedin-writer-comagency-plugins/`. In Cowork, ask the writer agent to `ls ${CLAUDE_PLUGIN_DATA}` to confirm.
- **LinkedIn URL didn't work during setup:** LinkedIn blocks unauthenticated fetches. Paste the post text instead.
- **Post ignored a format rule:** the priority hierarchy puts Tone above Format on purpose. If Tone examples in your config show 200-word posts, the writer will lean toward 200 words even if format rules say 150. Tighten the tone examples or loosen the format rule.

## Feedback

Issues and pull requests welcome at [alinusgallus/comagency-plugins](https://github.com/alinusgallus/comagency-plugins).
