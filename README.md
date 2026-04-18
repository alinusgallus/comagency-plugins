# ComAgency Plugins

A Claude Code plugin marketplace for content-agency workflows.

## Plugins

| Plugin | Description |
|--------|-------------|
| [`linkedin-writer`](plugins/linkedin-writer/) | Conversational LinkedIn post generator. Setup agent establishes brand context and reusable post-type presets from your own materials; writer agent produces on-brand posts from a short brief. |

## Install

Once this repo is pushed to GitHub, anyone with access can add the marketplace and install a plugin in two commands inside Claude Code:

```
/plugin marketplace add <owner>/comagency-plugins
/plugin install linkedin-writer@comagency-plugins
```

Replace `<owner>` with the GitHub owner/organisation you push this repo under. For a private repo, the user needs `gh auth login` or SSH keys configured for that repo.

## Local install (before pushing)

From any directory:

```
/plugin marketplace add /Users/alaingall/Dev/comagency-plugins
/plugin install linkedin-writer@comagency-plugins
```

Or mount the plugin directly without going through the marketplace layer:

```bash
claude --plugin-dir /Users/alaingall/Dev/comagency-plugins/plugins/linkedin-writer
```

## Update a plugin

```
/plugin marketplace update comagency-plugins
```

Plugins declare their version in `plugins/<name>/.claude-plugin/plugin.json`. Bump that field and push before users run the update command.

## Auto-install for a team project

In any project that should ship with these plugins, add to `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "comagency-plugins": {
      "source": {
        "source": "github",
        "repo": "<owner>/comagency-plugins"
      }
    }
  },
  "enabledPlugins": {
    "linkedin-writer@comagency-plugins": true
  }
}
```

Teammates who trust the project directory get prompted to install on first launch.

## Repository layout

```
comagency-plugins/
├── .claude-plugin/
│   └── marketplace.json    # Marketplace manifest — lists published plugins
├── plugins/
│   └── linkedin-writer/    # One directory per plugin
│       ├── .claude-plugin/plugin.json
│       ├── agents/
│       └── README.md
└── README.md
```

## Adding a new plugin

1. Create `plugins/<name>/` with its own `.claude-plugin/plugin.json` and contents (agents, skills, hooks, MCP servers).
2. Add an entry to the `plugins` array in `.claude-plugin/marketplace.json`.
3. Commit and push.
4. Users run `/plugin marketplace update comagency-plugins` and then `/plugin install <name>@comagency-plugins`.
