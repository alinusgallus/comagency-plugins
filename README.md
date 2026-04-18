# ComAgency Plugins

A small Claude Code plugin marketplace for content-agency workflows. Install once, use from any project.

## Available plugins

| Plugin | What it does |
|--------|--------------|
| [`linkedin-writer`](plugins/linkedin-writer/) | Write on-brand LinkedIn posts from a short brief. Learns your voice from materials you already have. Runs in Claude Cowork and local Claude Code; state persists across sessions. No API keys. |

## Install

Inside Claude Code:

```
/plugin marketplace add alinusgallus/comagency-plugins
/plugin install linkedin-writer@comagency-plugins
```

See each plugin's own README for usage — the `linkedin-writer` README has a full walkthrough of the first-run flow.

## Updates

```
/plugin marketplace update comagency-plugins
```

## Auto-install for a team project

If every teammate on a project should have a plugin installed, add it to the project's `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "comagency-plugins": {
      "source": {
        "source": "github",
        "repo": "alinusgallus/comagency-plugins"
      }
    }
  },
  "enabledPlugins": {
    "linkedin-writer@comagency-plugins": true
  }
}
```

Teammates who trust the project directory get prompted to install on first launch.

## Local development

To iterate on a plugin without pushing:

```
/plugin marketplace add /path/to/comagency-plugins
/plugin install linkedin-writer@comagency-plugins
```

Or mount a plugin directly:

```bash
claude --plugin-dir /path/to/comagency-plugins/plugins/linkedin-writer
```

Run `/reload-plugins` inside Claude Code after editing plugin files.

## Adding a new plugin

1. Create `plugins/<name>/` with its own `.claude-plugin/plugin.json` and contents (agents, skills, hooks, MCP servers).
2. Add an entry to the `plugins` array in `.claude-plugin/marketplace.json`.
3. Bump the plugin's `version` in `plugin.json` on every user-visible change.
4. Commit and push. Users pick it up with `/plugin marketplace update comagency-plugins`.

## Repository layout

```
comagency-plugins/
├── .claude-plugin/
│   └── marketplace.json    # Lists published plugins
├── plugins/
│   └── linkedin-writer/
│       ├── .claude-plugin/plugin.json
│       ├── agents/
│       └── README.md
└── README.md
```
