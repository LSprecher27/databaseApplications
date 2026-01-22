# SQL Reference Guide (NBA Dataset)
**Database Applications Development | Medina County Career Center**

This guide mirrors the structure of the original SQL reference (Titanic version) but swaps in **NBA tables/columns** and includes examples that match the kinds of questions you’re doing in the DB Apps NBA tasks.

---

## Table of Contents
1. [Your NBA Tables (Typical)](#your-nba-tables-typical)
2. [Basic Query Structure](#basic-query-structure)
3. [SELECT - Choosing Columns](#select---choosing-columns)
4. [WHERE - Filtering Rows](#where---filtering-rows)
5. [ORDER BY + LIMIT - Top N Queries](#order-by--limit---top-n-queries)
6. [Table Aliases (b., t., ps., tgs.)](#table-aliases-b-t-ps-tgs)
7. [JOIN - Combining Tables](#join---combining-tables)
8. [Aggregate Functions (COUNT, SUM, AVG, MIN, MAX)](#aggregate-functions-count-sum-avg-min-max)
9. [GROUP BY - Team or Player Summaries](#group-by---team-or-player-summaries)
10. [CASE WHEN - Wins/Losses and Conditional Counts](#case-when---winslosses-and-conditional-counts)
11. [HAVING - Filtering Groups](#having---filtering-groups)
12. [Export Patterns (pandas → Excel)](#export-patterns-pandas--excel)
13. [Troubleshooting (Common Errors)](#troubleshooting-common-errors)

---

## Your NBA Tables (Typical)

Your exact column names come from your walkthrough/schema. If you’re ever unsure, run:

```sql
PRAGMA table_info(teams);
PRAGMA table_info(team_game_stats);
PRAGMA table_info(players);
PRAGMA table_info(player_season_stats);
```

**Common columns used in our class examples:**
- `teams`: `team_id`, `full_name`, `city`, `state`, `conference`
- `team_game_stats`: `game_id`, `team_id`, `season`, `game_date`, `matchup`, `pts`, `wl`
- `players`: `player_id`, `full_name`
- `player_season_stats`: `player_id`, `team_id`, `season`, `gp`, `pts`, `reb`, `ast`

If your database uses slightly different names, **swap them in** (the query patterns still apply).

---

## Basic Query Structure

```sql
SELECT column1, column2
FROM table_name
WHERE condition
ORDER BY column
LIMIT number;
```

**Write clauses in this order:**
1. `SELECT`
2. `FROM`
3. `JOIN` (optional)
4. `WHERE` (optional)
5. `GROUP BY` (optional)
6. `HAVING` (optional)
7. `ORDER BY` (optional)
8. `LIMIT` (optional)

---

## SELECT - Choosing Columns

### Select all columns (quick peek)
```sql
SELECT *
FROM teams
LIMIT 5;
```

### Select specific columns
```sql
SELECT full_name, conference
FROM teams
ORDER BY conference, full_name;
```

### Rename output columns with AS
```sql
SELECT full_name AS team_name, conference
FROM teams;
```

---

## WHERE - Filtering Rows

### Text needs single quotes
```sql
SELECT *
FROM team_game_stats
WHERE season = '2021-22'
LIMIT 10;
```

### Numbers do not use quotes
```sql
SELECT *
FROM team_game_stats
WHERE pts >= 120
  AND season = '2021-22';
```

### IS NULL / IS NOT NULL
```sql
SELECT *
FROM team_game_stats
WHERE game_date IS NOT NULL
LIMIT 10;
```

---

## ORDER BY + LIMIT - Top N Queries

### Top 10 highest-scoring team games
```sql
SELECT team_id, game_date, pts
FROM team_game_stats
WHERE season = '2021-22'
ORDER BY pts DESC
LIMIT 10;
```

### “Top N” pattern reminder
Filtering → Sorting → Limiting

---

## Table Aliases (b., t., ps., tgs.)

Aliases are **nicknames for tables** inside one query.

```sql
FROM team_game_stats tgs
JOIN teams t ON tgs.team_id = t.team_id
```

Now:
- `tgs.pts` means `team_game_stats.pts`
- `t.full_name` means `teams.full_name`

Even though `SELECT` is written before `FROM`, SQL **reads the whole query first**, then resolves aliases. (Aliases do **not** carry to the next query.)

---

## JOIN - Combining Tables

### Join team names onto game stats (most common pattern)
```sql
SELECT
  t.full_name AS team_name,
  tgs.game_date,
  tgs.matchup,
  tgs.pts,
  tgs.wl
FROM team_game_stats tgs
JOIN teams t ON tgs.team_id = t.team_id
WHERE tgs.season = '2021-22'
LIMIT 10;
```

### Join player names onto player season stats
```sql
SELECT
  p.full_name AS player_name,
  ps.season,
  ps.gp,
  ps.pts
FROM player_season_stats ps
JOIN players p ON ps.player_id = p.player_id
WHERE ps.season = '2021-22'
LIMIT 10;
```

**Tip:** If you ever see `no such column: p.team_id`, that usually means the `players` table **does not store** team_id (team membership changes). Use `player_season_stats` to connect players ↔ teams.

---

## Aggregate Functions (COUNT, SUM, AVG, MIN, MAX)

Aggregates summarize many rows into fewer rows.

### Summary stats for a season
```sql
SELECT
  COUNT(*) AS games_rows,
  ROUND(AVG(pts), 1) AS avg_points,
  MIN(pts) AS min_points,
  MAX(pts) AS max_points
FROM team_game_stats
WHERE season = '2021-22';
```

### Average team points per game (by team)
```sql
SELECT
  t.full_name AS team_name,
  COUNT(tgs.game_id) AS games_played,
  ROUND(AVG(tgs.pts), 1) AS avg_points
FROM team_game_stats tgs
JOIN teams t ON tgs.team_id = t.team_id
WHERE tgs.season = '2021-22'
GROUP BY t.team_id, t.full_name
ORDER BY avg_points DESC;
```

---

## GROUP BY - Team or Player Summaries

### Rule: every non-aggregated column in SELECT must be in GROUP BY
✅ Correct:
```sql
SELECT
  team_id,
  season,
  ROUND(AVG(pts), 1) AS avg_points
FROM team_game_stats
GROUP BY team_id, season;
```

❌ Incorrect (season missing from GROUP BY):
```sql
SELECT team_id, season, AVG(pts)
FROM team_game_stats
GROUP BY team_id;
```

---

## CASE WHEN - Wins/Losses and Conditional Counts

This is the “count only some rows” trick.

### Win-loss record per team
```sql
SELECT
  t.full_name AS team_name,
  COUNT(tgs.game_id) AS games_played,
  SUM(CASE WHEN tgs.wl = 'W' THEN 1 ELSE 0 END) AS wins,
  SUM(CASE WHEN tgs.wl = 'L' THEN 1 ELSE 0 END) AS losses
FROM team_game_stats tgs
JOIN teams t ON tgs.team_id = t.team_id
WHERE tgs.season = '2021-22'
GROUP BY t.team_id, t.full_name
ORDER BY wins DESC;
```

**What’s happening:**
- `CASE WHEN wl='W' THEN 1 ELSE 0 END` turns each row into a 1 (win) or 0 (not win).
- `SUM(...)` adds those 1s up → total wins.

---

## HAVING - Filtering Groups

`WHERE` filters rows **before** grouping.  
`HAVING` filters groups **after** aggregation.

### Only show teams averaging 115+ points
```sql
SELECT
  t.full_name AS team_name,
  ROUND(AVG(tgs.pts), 1) AS avg_points
FROM team_game_stats tgs
JOIN teams t ON tgs.team_id = t.team_id
WHERE tgs.season = '2021-22'
GROUP BY t.team_id, t.full_name
HAVING AVG(tgs.pts) >= 115
ORDER BY avg_points DESC;
```

---

## Export Patterns (pandas → Excel)

### Pattern A: Write SQL → run it → export to Excel
```python
query = """
SELECT
  t.full_name AS team_name,
  COUNT(tgs.game_id) AS games_played,
  ROUND(AVG(tgs.pts), 1) AS avg_points
FROM team_game_stats tgs
JOIN teams t ON tgs.team_id = t.team_id
WHERE tgs.season = '2021-22'
GROUP BY t.team_id, t.full_name
ORDER BY avg_points DESC
"""

df = pd.read_sql(query, conn)
df.to_excel("team_performance.xlsx", index=False, sheet_name="Team Stats")
```

### Pattern B: “Top scorers” using season stats (total points = gp * pts)
```python
query = """
SELECT
  p.full_name AS player_name,
  t.full_name AS team_name,
  ps.gp AS games_played,
  ROUND(ps.gp * ps.pts, 0) AS total_points,
  ps.pts AS points_per_game
FROM player_season_stats ps
JOIN players p ON ps.player_id = p.player_id
JOIN teams t   ON ps.team_id = t.team_id
WHERE ps.season = '2021-22'
  AND ps.gp >= 20
ORDER BY total_points DESC
LIMIT 50
"""
```

**What’s happening:**
- In season tables, `pts` is often **points per game**, so total points is `gp * pts`.
- `ROUND(..., 0)` just cleans up the display.

---

## Troubleshooting (Common Errors)

### 1) `TypeError: 'NoneType' object is not iterable` (pandas read_sql)
Almost always means your query string is empty:

```python
query = """ """   # <-- empty
pd.read_sql(query, conn)
```

Fix: put a real `SELECT ...` in the triple quotes.

---

### 2) `OperationalError: no such column: p.player_name`
Column name doesn’t exist. Check actual columns:

```sql
PRAGMA table_info(players);
```

Then swap to the real column (often `full_name`).

---

### 3) `OperationalError: no such column: p.team_id`
Your `players` table likely **does not have** a `team_id`.  
Use the relationship table (`player_season_stats` or similar) to connect players ↔ teams.

---

### 4) “Why does it reference `tgs.` before it’s defined?”
SQL is not executed top-to-bottom like Python. The database parses the whole statement, then resolves aliases from the `FROM`/`JOIN` clauses.

---

## Quick “Print & Keep” Card

```text
SELECT ...                what columns
FROM ...                  what table
JOIN ... ON ...           connect tables (optional)
WHERE ...                 filter rows (optional)
GROUP BY ...              make groups (optional)
HAVING ...                filter groups (optional)
ORDER BY ...              sort (optional)
LIMIT ...                 top N (optional)
```

Keep this guide open while you work—pros look up syntax all the time.
