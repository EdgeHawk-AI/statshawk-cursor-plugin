---
name: statshawk-check
description: Smoke-test the StatsHawk plugin connection and core tool paths
---

Run a bounded StatsHawk smoke check. Do not invent numbers. Stop after
the listed calls. On 401, tell the user to reconnect StatsHawk in
Customize and stop. On `QUOTA_EXCEEDED`, stop — do not add box-score
calls.

1. Confirm the StatsHawk MCP tools are available. If not, say the plugin
   is installed but the MCP server is not connected.
2. `search_player` with a well-known MLB name (e.g. "Aaron Judge").
3. `get_standings` for `mlb`.
4. `get_player_props` for that person, `league=mlb`, `stat=h`, `line=1.5`.
   Expect a card on every plan, including Free. Report
   `hit_rates.last_10.games` (or `season` if last_10 is absent), not the
   top-level `games` field. Do not invent a through-date.
5. Summarize: connected or not, and any tool that failed. Retry a single
   transient 5xx at most twice; never retry 401 or 429.
