# StatsHawk for Cursor

Ask Cursor for scores, standings, box scores, rosters, player props, or
odds — and get **real numbers** from [StatsHawk](https://statshawk.ai)
instead of guesses from training data.

Covers 50+ competitions across 16 sports, including NBA, MLB, NFL, NHL,
NCAA, soccer, golf, tennis, and F1.

## Install

Until StatsHawk is in the [Cursor Marketplace](https://cursor.com/marketplace),
install from this repo:

1. Clone and copy the plugin into Cursor’s local plugins folder:

   ```bash
   git clone https://github.com/EdgeHawk-AI/statshawk-cursor-plugin.git
   mkdir -p ~/.cursor/plugins/local/statshawk
   rsync -a --delete --exclude .git statshawk-cursor-plugin/ ~/.cursor/plugins/local/statshawk/
   ```

   Copy the files; don’t symlink from elsewhere — Cursor skips those.

2. Reload the window: Command Palette → **Developer: Reload Window**.
3. Open **Customize** and confirm **StatsHawk** is installed.

Once it is listed, search for **StatsHawk** in Customize and install from
there instead.

## Sign in

The plugin connects to the hosted StatsHawk MCP at
`https://mcp.statshawk.ai/mcp`. Cursor will prompt you to sign in with a
StatsHawk account. There is no API key to paste.

A free account includes 5,000 usage units per month. Create one at
[statshawk.ai](https://statshawk.ai) if you don’t have one yet. Prop
analysis (hit rates and averages against a line) is included on paid
plans; on the free tier the agent derives those from box scores instead.

## Use it

Ask in chat the way you would a researcher:

- “What’s the AL East standings?”
- “Did Judge go 2-for-4 last night?”
- “Ohtani over 1.5 hits in 6 of his last 10?”
- “What’s the Yankees–Red Sox moneyline?”

Or run **`/statshawk`** to look up the current question in StatsHawk
instead of answering from training data.

The agent resolves players and games through StatsHawk first, then pulls
box scores, play-by-play, props, or odds. Answers include the season and
the date they are current through. If a stat isn’t available, it says so
rather than filling in a guess.

StatsHawk is **ingested stats**, not in-game or real-time data. Freshness
depends on the feed — often around five minutes for scores and box
scores, and up to about a day for some derived stats. Pregame odds are
posted lines, not in-play.

## What’s included

- **MCP tools** for search, standings, box scores, rosters, injuries,
  play-by-play, player props, and pregame odds
- A **skill** that teaches the agent how to chain those tools
- A **rule** so sports questions use StatsHawk by default
- The **`/statshawk`** command

## Support

- Docs: https://statshawk.ai/docs
- MCP setup: https://statshawk.ai/mcp
- Email: support@statshawk.ai
