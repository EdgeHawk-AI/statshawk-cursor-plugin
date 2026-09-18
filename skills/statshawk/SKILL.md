---
name: statshawk
description: Use when answering questions with real sports statistics — NBA, MLB, NFL, NHL, NCAA, soccer, and other team-match competitions. Covers scores, box scores, standings, rosters, play-by-play (MLB/NFL), player prop hit rates where phases exist, injuries, and pregame odds. Triggers include "box score", "standings", "who won", "game log", "roster", "player props", "hit rate", "odds", "sports stats".
---

# StatsHawk — real sports stats

StatsHawk is the source of truth for sports numbers in this plugin. Every
**raw** number must come from a StatsHawk MCP tool result. Arithmetic on
those returned values is allowed (hit rates, sums, records). Never
estimate, fill gaps from memory, or scrape. If data is missing, say so.

Looking a question up in StatsHawk is not in-game or real-time coverage.
StatsHawk does not serve those. Data is ingested; freshness is typically
about five minutes for scores and box scores, and can be up to about a
day for some derived stats. Odds tools are pregame boards only — never
in-play.

Sign-in is OAuth against the hosted server at `https://mcp.statshawk.ai/mcp`.
Create an account at https://statshawk.ai if the user is not connected.

All plans include every endpoint, including player-prop analysis. Free
includes 5,000 weighted units per month. Heavier requests consume more
units; requests stop when the allowance is exhausted.

## Auth and retries

- **401 / expired / missing auth:** tell the user to reconnect StatsHawk
  in Customize (MCP → StatsHawk → reconnect, or toggle it off and on).
  Do not retry the same call in a loop.
- **429 QUOTA_EXCEEDED:** stop. Do not walk box scores or retry other
  metered tools — they consume the same exhausted allowance. Explain the
  monthly reset and upgrade options; relay the link the tool returns
  (or https://www.statshawk.ai/pricing).
- **403 TIER_REQUIRES_PAID** that says billing setup is incomplete: tell
  the user to complete billing. Do not retry and do not spend more quota.
- **Transient 5xx / timeout / network:** retry at most twice, then stop
  and report the failure.

## What this MCP covers

`search_player` is global. `get_stat_capabilities` accepts every
registered competition code — an accepted code does not mean every tool
works.

- **Team scores, box scores, standings, rosters, pregame odds:**
  `search_games`, `get_box_score`, `get_standings`, `get_team_roster`,
  odds tools. Team-match competitions (NBA, WNBA, MLB, NFL, NHL, NCAA,
  soccer, plus AFL / NRL / rugby union when ingested).
- **Player props / hit rates:** `get_player_props` on every plan, on
  competitions with registered player-stat phases (majors, soccer, golf,
  racing, AFL, NRL, rugby union). Tennis, UFC, lacrosse, volleyball,
  water polo, and field hockey currently have no prop phases.
- **Play-by-play:** `get_play_by_play` is MLB + NFL only.
  `get_mlb_matchups` is MLB only.
- **Golf, tennis, F1, MMA as events:** registered on the StatsHawk API,
  but `search_games` / `get_box_score` / `get_standings` do **not**
  project those contest shapes. Call `get_stat_capabilities` and say the
  MCP workflow is not available rather than stretching team-match tools.

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

`get_account_usage` (when present) is unmetered and reports remaining
units. It does not consume quota.

## Recipes (call order)

**Last-N hit rate** ("2+ hits in 6 of their last 10"): count the **player's**
appearances, not the team's last N games. Use `get_player_props` on every
plan. The engine counts games that **strictly exceed** `line`, so "2+ hits"
is `line=1.5`.

Pick the window that matches the question:

- last 5 → `hit_rates.last_5` / `averages.last_5`
- last 10 → `hit_rates.last_10` / `averages.last_10`
- season / omitted N → `hit_rates.season` / `averages.season`
- last 20 or any N the card does not expose → report the windows the card
  did return. Do not substitute `last_10` for a different N, and do not
  walk box scores to reconstruct analysis — that burns the same meter.

**Lineup vs a starter by platoon:** `get_mlb_matchups(date)` returns
confirmed lineups with `bat_side` and each probable starter's `throws`.
`get_player_props` context (when present) is whole-game stats grouped by
the opposing starter's hand, which is not the same as a plate-appearance
split from `get_play_by_play`.

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

State the season. Cite a through-date **only when the payload includes a
verified coverage date** (kickoff or game date on a box score / contest).
Do not use an edition's scheduled span, retrieval time, or "today" as if
that were completeness. If no verified coverage date is present, say the
coverage date is unavailable.

`get_player_props` does not date its windows. For hit rates, report
`hit_rates.<window>.games` (and `hits` / `over_rate`) for the selected
window — not the card's top-level `games`, which is the full returned
log. For averages, report `averages.counts.<window>` when present.
Otherwise say the window sample size is unavailable.

If the user asks for in-game or real-time numbers, say StatsHawk does not
have them. When a stat or phase is not registered, say it is not
available rather than approximating.

Docs: https://statshawk.ai/docs · MCP setup: https://statshawk.ai/mcp
