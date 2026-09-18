# StatsHawk for Cursor

Cursor plugin that gives the agent **real sports statistics** from
[StatsHawk](https://statshawk.ai) instead of guesses from training data.

It bundles:

- **MCP server** — hosted at `https://mcp.statshawk.ai/mcp` (OAuth; free
  tier is 5,000 units/month)
- **Skill** — id chaining, hit-rate recipes, and plan-aware fallbacks
- **Rule** — use StatsHawk for scores, standings, box scores, props, odds
- **Command** — `/sports-stats` to force a live lookup

Coverage is 50+ competitions across 16 sports (NBA, MLB, NFL, NHL, NCAA,
soccer, golf, tennis, F1, and more). Tool availability per league is
discoverable at runtime via `get_stat_capabilities`.

## Install

Once listed on the [Cursor Marketplace](https://cursor.com/marketplace),
search for **StatsHawk** in Customize and install.

Until then, test from this repo:

```bash
mkdir -p ~/.cursor/plugins/local/statshawk
rsync -a --delete --exclude .git ./ ~/.cursor/plugins/local/statshawk/
```

Then reload the window (`Developer: Reload Window`) and confirm **StatsHawk**
appears in Customize. Connect the MCP server when Cursor prompts for OAuth.

Do not symlink the repo into `~/.cursor/plugins/local` — Cursor only loads
symlinks whose target is already inside that folder.

## What's inside

```text
.
├── .cursor-plugin/plugin.json   # Marketplace manifest
├── mcp.json                     # Hosted StatsHawk MCP
├── skills/statshawk/SKILL.md    # When and how to call the tools
├── rules/use-real-sports-stats.mdc
├── commands/sports-stats.md
├── assets/logo.png
└── README.md
```

The plugin does not ship API keys. Auth is the same OAuth flow as connecting
StatsHawk in Cursor Settings → MCP.

## Submit to the marketplace

1. Push this repo to a **public** GitHub repository (company org preferred).
2. Test locally as above.
3. Submit the repo URL at https://cursor.com/marketplace/publish
4. In the submission notes, identify **StatsHawk** as the company publisher
   and give `support@statshawk.ai` as the contact. The form currently binds
   Owner to the logged-in Cursor account; the `author` field in
   `plugin.json` is what should appear on the listing.

After listing, request a re-index for updates — do not file a new application.

## Related

- Hosted MCP: https://statshawk.ai/mcp
- API docs: https://statshawk.ai/docs
- Claude skills package: https://github.com/EdgeHawk-AI/statshawk-skills
