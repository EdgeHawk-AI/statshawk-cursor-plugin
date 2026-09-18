---
name: statshawk
description: Look up the current sports question in StatsHawk instead of answering from memory
---

Use the StatsHawk MCP tools to answer the user's sports question with
ingested StatsHawk numbers. Do not estimate. Arithmetic on tool-returned
values is allowed. Do not call the result live or real-time — StatsHawk
is not in-game data; freshness varies from about five minutes to about a
day depending on the stat.

All plans include every endpoint, including player-prop analysis. Free
includes 5,000 weighted units per month. Heavier requests consume more
units; requests stop when the allowance is exhausted.

1. Identify the league, players, teams, and date range. If the sport is
   golf, tennis, F1, or MMA as an event, check `get_stat_capabilities`
   and do not force `search_games` / `get_box_score`.
2. Resolve ids (`search_player`, `search_games`, `get_standings`) before
   fetching box scores, props, play-by-play, or odds.
3. Use `get_player_props` for averages and hit rates on every plan. Match
   `hit_rates.last_5` / `last_10` / `season` to the requested window; do
   not substitute last-10 for a different N.
4. Cite season. For props, report `hit_rates.<window>.games` (or
   `averages.counts.<window>`), not the card's top-level `games`. Cite a
   through-date only when the payload has a verified coverage date
   (kickoff or game date), never an edition span. Odds are pregame only.
5. On 401, tell the user to reconnect StatsHawk in Customize. Retry
   transient 5xx at most twice. On `QUOTA_EXCEEDED`, stop — do not walk
   box scores. If billing setup is incomplete, tell the user to complete
   it.
