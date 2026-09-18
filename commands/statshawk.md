---
name: statshawk
description: Look up the current sports question in StatsHawk instead of answering from memory
---

Use the StatsHawk MCP tools to answer the user's sports question with
ingested StatsHawk numbers. Do not estimate. Do not call the result live
or real-time — StatsHawk is not in-game data; freshness varies from about
five minutes to about a day depending on the stat.

1. Identify the league, players, teams, and date range.
2. Resolve ids (`search_player`, `search_games`, `get_standings`) before
   fetching box scores, props, play-by-play, or odds.
3. Prefer `get_player_props` for averages and hit rates. On
   `TIER_REQUIRES_PAID`, derive from box scores instead and say so.
4. Cite the season, the date the data is current through, and the sample
   size. Odds are pregame only.
