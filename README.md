# StatsHawk for Cursor

Ask Cursor for scores, standings, box scores, rosters, player props, or
odds — and get **real numbers** from [StatsHawk](https://statshawk.ai)
instead of guesses from training data.

## Coverage

The hosted MCP is a subset of the StatsHawk API. An accepted competition
code does not mean every tool works.

| Workflow | MCP tools | Where it works |
| --- | --- | --- |
| Team scores, box scores, standings, rosters, pregame odds | `search_games`, `get_box_score`, `get_standings`, `get_team_roster`, odds tools | Team-match competitions: NBA, WNBA, MLB, NFL, NHL, NCAA, soccer, plus AFL / NRL / rugby union when ingested |
| Player search and stat keys | `search_player`, `get_stat_capabilities` | All registered competitions |
| Player props / hit rates | `get_player_props` | Competitions with player-stat phases (majors, soccer, golf, racing, AFL, NRL, rugby union). Not tennis, UFC, lacrosse, volleyball, water polo, or field hockey |
| Play-by-play | `get_play_by_play` | MLB and NFL only |
| Golf, tennis, F1, MMA as events | — | On the StatsHawk API; `search_games` / `get_box_score` / `get_standings` do not project those contest shapes yet |

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

Create a free account at [statshawk.ai](https://statshawk.ai) if you don’t
have one yet. All plans include every endpoint, including player-prop
analysis. Free includes 5,000 weighted units per month. Heavier requests
consume more units; requests stop when the allowance is exhausted.

## Use it

Ask in chat the way you would a researcher:

- “What’s the AL East standings?”
- “Did Judge go 2-for-4 last night?”
- “Ohtani over 1.5 hits in 6 of his last 10?”
- “What’s the Yankees–Red Sox moneyline?”

Or run **`/statshawk`** to look up the current question in StatsHawk
instead of answering from training data. **`/statshawk-check`** runs a
bounded connection smoke test.

The agent resolves players and games through StatsHawk first, then pulls
box scores, play-by-play, props, or odds. Answers include the season and,
when a payload has a verified coverage date (kickoff or game date), that
date. If a stat isn’t available, it says so rather than filling in a
guess.

StatsHawk is **ingested stats**, not in-game or real-time data. Freshness
depends on the feed — often around five minutes for scores and box
scores, and up to about a day for some derived stats. Pregame odds are
posted lines, not in-play.

## What’s included

- **MCP tools** for search, standings, box scores, rosters, injuries,
  play-by-play, player props, and pregame odds
- A **skill** that teaches the agent how to chain those tools
- A **rule** so sports questions use StatsHawk by default
- The **`/statshawk`** and **`/statshawk-check`** commands

## Check that it works

1. Install and reload as above.
2. Sign in when Cursor prompts for StatsHawk OAuth.
3. Run **`/statshawk`** on a standings question (AL East).
4. Ask a prop question (Ohtani last 10 hits). Expect `get_player_props`
   on every plan, including Free.
5. If tools return 401, reconnect StatsHawk in Customize. If they return
   `QUOTA_EXCEEDED`, stop — more box-score calls use the same allowance.

## Support

- Docs: https://statshawk.ai/docs
- MCP setup: https://statshawk.ai/mcp
- Pricing: https://www.statshawk.ai/pricing
- Email: support@statshawk.ai
