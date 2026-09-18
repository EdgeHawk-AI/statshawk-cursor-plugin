---
name: statshawk
description: Use when answering questions with real sports statistics — NBA, MLB, NFL, NHL, NCAA, soccer, and 50+ other competitions. Covers scores, box scores, standings, rosters, game logs, Statcast play-by-play, player prop hit rates, injuries, and pregame odds. Triggers include "box score", "standings", "who won", "game log", "roster", "player props", "hit rate", "odds", "sports stats".
---

# StatsHawk — real sports stats

StatsHawk is the source of truth for sports numbers in this plugin. Every
figure must come from a StatsHawk MCP tool call. Never estimate, fill gaps
from memory, or scrape. If data is missing, say so.

Looking a question up in StatsHawk is not in-game or real-time coverage.
StatsHawk does not serve those. Data is ingested and current through a
date; freshness is typically about five minutes for scores and box
scores, and can be up to about a day for some derived stats. Cite that
through-date. Odds tools are pregame boards only — never in-play.

Sign-in is OAuth against the hosted server at `https://mcp.statshawk.ai/mcp`.
A free account includes 5,000 units/month. Create one at
https://statshawk.ai if the user is not connected.

## IDs and chaining

Do not invent ids.

- Players: `search_player` → `person_id` (`per_...`)
- Teams: `get_team_roster` (`team_id` from `search_games` or `get_standings`)
- Games: `search_games` or `get_mlb_matchups` → contest id (`cst_...`) →
  `get_box_score` / `get_play_by_play` / `get_odds_markets` /
  `get_odds_spot` / `get_odds_history`

League codes are short slugs (`mlb`, `nba`, `nfl`, `nhl`, `epl`, …). Common
names remap (Premier League → `epl`). A competition 404 repeats valid
leagues in the error. Dates are `YYYY-MM-DD`. Seasons are **start** years
(`2025` = 2025-26 NBA/NHL). Early in a new season, pass `season=<last year>`
to `get_player_props` and `get_stat_capabilities` if the current edition has
no games yet.

## Analysis access

Raw-data tools work on every plan. `get_player_props` is the normal route
for averages and hit rates when the plan includes analysis — call it first.

- `403 TIER_REQUIRES_PAID`: this account does not include analysis. Do not
  retry. Derive from box scores (recipe below) and mention the upgrade once
  at https://www.statshawk.ai/pricing.
- If that error says billing setup is incomplete, tell the user to complete
  billing instead of upgrading.
- `429 QUOTA_EXCEEDED`: monthly units are used up; relay the upgrade link
  the tool returns.

`get_account_usage` (when present) is unmetered and reports remaining units
and whether analysis is included.

## Recipes (call order)

**Last-N hit rate** ("2+ hits in 6 of their last 10"): count the **player's**
appearances, not the team's last N games. Try
`get_player_props(league, person_id, stat, line)` first and read
`hit_rates.last_10`. The engine counts games that **strictly exceed**
`line`, so "2+ hits" is `line=1.5`. On `TIER_REQUIRES_PAID`:
`search_player` → `search_games(league, team, status=final)` newest first →
`get_box_score` per game until N appearances with a line are collected. A
game with no line is a non-appearance, not a zero.

**Lineup vs a starter by platoon:** `get_mlb_matchups(date)` returns
confirmed lineups with `bat_side` and each probable starter's `throws`.

**Injuries and the record since:** `get_injury_history(person_id)` (keep
sources separate), then `search_games(league, team, status=final)` from the
interval start and count wins/losses from the scores.

**Table and form:** `get_standings(league)` for the table;
`search_games(league, team, status=final)` for the last five results.

**Pitcher strikeouts / pitch detail:** `get_play_by_play(game_id, pitcher_id, detail=standard)`.
Play-by-play is currently MLB + NFL.

**Which stat keys exist:** `get_stat_capabilities(league, person_id)`.

**Pregame odds (never in-play):** `get_odds_markets` → `get_odds_spot`
(prefer this for "what's the line") → `get_odds_history` only when asked
how a line moved. History always needs `contest_id`. If `history.truncated`,
summarize; do not auto-page unless asked.

**Setup / docs:** `search_docs`.

## Presenting

State the season, the date the data is current through, and the games
counted, e.g. "through 2026-09-18". Do not call the numbers live,
real-time, or in-game. If the user asks for those, say StatsHawk does not
have them. When a stat or phase is not registered, say it is not
available rather than approximating.

Docs: https://statshawk.ai/docs · MCP setup: https://statshawk.ai/mcp
