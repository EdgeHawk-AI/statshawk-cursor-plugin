---
name: statshawk
description: Answer the current sports question with live StatsHawk data
---

Use the StatsHawk MCP tools to answer the user's sports question with real
numbers. Do not estimate.

1. Identify the league, players, teams, and date range.
2. Resolve ids (`search_player`, `search_games`, `get_standings`) before
   fetching box scores, props, play-by-play, or odds.
3. Prefer `get_player_props` for averages and hit rates. On
   `TIER_REQUIRES_PAID`, derive from box scores instead and say so.
4. Cite the season, date range, and sample size in the answer.
