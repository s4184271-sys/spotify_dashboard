# spotify_dashboard
# Most Streamed Artists on Spotify — Power BI Dashboard

A dark, Spotify-themed interactive dashboard built in Power BI, analyzing the 500 most streamed artists on Spotify (data as of 17 July 2026). The dashboard explores stream composition, artist demographics, genre/language trends, and geographic origin.

![Theme](https://img.shields.io/badge/tool-Power%20BI-F2C811) ![Theme](https://img.shields.io/badge/theme-Spotify%20Dark-1ED760)

---

## Dataset

- **Source**: Kaggle — *Most Streamed Artists on Spotify*
- **Records**: 500 artists
- **Fields**: Artist Name, Sex, Country of Origin, Primary Genre, Primary Language, Artist Type (Solo/Group), Debut Year, Total/Lead/Feature/Solo/Collaborative Streams (in millions)

## Data Cleaning (Power Query)

- Fixed a leading-space bug in the `Artist Type` column header
- Verified no missing values or duplicate artist names
- Corrected an incorrect **Date** data type on `Debut Year` (was misread as a date serial number) back to **Whole Number**
- Removed thousands-separator formatting so years display cleanly (e.g., `2013`, not `2,013`)

## Filters / Slicers

| Slicer | Style | Notes |
|---|---|---|
| Artist Name | Dropdown + search | 500 values, quick lookup |
| Primary Genre | Dropdown + search | |
| Artist Type | Dropdown | Solo / Group |
| Country of Origin | Dropdown + search | 43 countries |
| Sex | Dropdown | |
| Primary Language | List | 11 languages |
| Debut Year | List (multi-select) | 1939–2023 |

## KPI Cards

**Row 1 — Composition by track type**
- Total Streams (Sum)
- Collaborative Streams (Sum)
- Solo Streams (Sum)
- Number of Artists (Distinct Count of Artist Name)
- Top Genre (custom DAX measure, see below)

**Row 2 — Composition by artist role**
- Lead Streams (Sum)
- Feature Streams (Sum)

*(Lead + Feature streams reconcile exactly to Total Streams — validated during build: 6.07M + 2.34M = 8.41M)*

## Key DAX Measure — Top Genre

Power BI has no built-in aggregation for "most frequent category," so this measure ranks genres by artist count and returns the top one:

```dax
Top Genre = 
CALCULATE(
SELECTEDVALUE('Most Streamed Artists on Spotify'[Primary Genre]),
TOPN(
1,
SUMMARIZE(
'Most Streamed Artists on Spotify',
'Most Streamed Artists on Spotify'[Primary Genre],
"GenreCount", COUNT('Most Streamed Artists on Spotify'[Artist Name])
),
[GenreCount], DESC
)
)
```

**How it works:**
1. `SUMMARIZE` groups the table by `Primary Genre` and counts artists in each genre
2. `TOPN(1, ..., DESC)` filters that summary down to just the single highest-count genre
3. `CALCULATE` applies that one-row filter as the new context, so `SELECTEDVALUE` returns just that genre's name

Result: **Hip-Hop** (115 artists) — dynamically updates if slicers filter the dataset.

## Visuals

- **Top 10 Artists by Streams** — horizontal bar chart, Top N filter on Total Streams
- **Solo vs Group** — donut chart, artist type share by count (74.7% Solo / 25.3% Group)
- **Top 10 Countries by Artist Count** — horizontal bar chart, Top N filter
- **Total Streams by Language** — area chart, shows English's dominant share with a long tail across 10 other languages

## Design

- Canvas: pure black (`#000000`)
- Cards / panels: dark gray (`#181818`)
- Accent: Spotify green (`#1ED760`)
- Secondary/muted tone: gray (`#535353`)
- Consistent 22pt callout values, borderless cards, Spotify logo anchoring the top-left

## Business Problems Solved

Framing the analysis around questions a music label, streaming platform, or talent scout might actually ask:

- **Which artists are driving the most streaming volume, and should we prioritize marketing spend or renewals around them?**
Answered via the Top 10 Artists by Streams bar chart. Drake (137.5K M), Taylor Swift (127.9K M), and Bad Bunny (125.9K M) lead by a wide margin — the top 3 alone account for a disproportionate share of total streams.

- **Are solo acts or bands/groups more commercially dominant on the platform?**
Answered via the Solo vs Group donut chart. Solo artists represent 74.7% of the catalog vs 25.3% Group — suggesting A&R and signing strategy should weight toward solo talent if streaming share is the goal.

- **Which countries should we focus regional marketing, licensing, or scouting efforts on?**
Answered via the Top 10 Countries by Artist Count bar chart. The United States dominates with 264 artists — more than 4x the next closest country (UK, 55) — showing heavy market concentration rather than a globally even spread.

- **Which language markets represent the biggest streaming opportunity or risk of over-reliance?**
Answered via the Total Streams by Language area chart. English-language streams dwarf every other language combined; Spanish is a distant second — useful for deciding where to invest in localization or non-English talent development.

- **Is the platform's content mix dominated by a single genre, creating catalog risk?**
Answered via the Top Genre KPI and Primary Genre slicer. Hip-Hop leads with 115 artists, but no single genre holds a majority — the catalog is genre-diverse rather than dependency-risk concentrated.

- **How much of an artist's success comes from their own lead releases vs. guest/feature appearances?**
Answered via the Lead Streams vs Feature Streams KPI cards. Lead streams (6.07M) vastly outweigh feature streams (2.34M) platform-wide — collaborations contribute meaningfully, but an artist's own lead catalog is still the primary driver.

- **Can a business user quickly drill into any single artist, era, or demographic without needing a data analyst on standby?**
Answered via 7 interactive slicers (Name, Genre, Type, Country, Sex, Language, Debut Year) enabling self-service exploration — e.g., filtering to just Reggaeton artists from Colombia, or artists who debuted in the 2010s, updates every visual instantly.

## Problems Solved (Build Log)

Real issues hit during the build, and how each was diagnosed and fixed:

| # | Problem | Root Cause | Solution |
|---|---|---|---|
| 1 | `Artist Type` column wouldn't work in formulas | Column name had a hidden leading space (`" Artist Type"`) | Renamed the column in Power Query, removing the space |
| 2 | KPI card value wouldn't turn green | Was editing "Background"/wrong section instead of the "Callout value"/"Data label" color setting | Located the correct font-color property under the card's Values section |
| 3 | Card had an unwanted divider line and mini bar icon | Power BI's default Card visual "Icon" feature was enabled and centered over the label | Turned off the built-in Icon/Divider section in Format visual |
| 4 | New DAX measure threw `Cannot find table` error | Manually typed table name (`'Table'`, then a guessed underscore version) didn't match Power BI's actual auto-generated table name | Used field autocomplete instead of typing table names manually, confirming the exact name from the Data pane tooltip |
| 5 | "Top Genre" card showed **Afrobeats** instead of the real top genre | Measure used `FIRSTNONBLANK`-style logic (alphabetically/positionally first genre), not frequency-based ranking | Rewrote the measure using `SUMMARIZE` + `TOPN` + `CALCULATE` to rank by actual artist count |
| 6 | New "Lead Streams" / "Feature Streams" cards showed duplicate values from other cards | Copy-pasted cards for speed, but left the old field attached instead of swapping it | Removed the old field from each card's Fields well and dragged in the correct one |
| 7 | Bar chart showed `0.14M` instead of the real value (`137,492`) | Power BI's auto **Display Units** re-scaled a column that was already labeled "in millions," double-converting it | Set Display Units to **None** on the axis/data labels |
| 8 | Debut Year slicer displayed nonsense dates like `22-04-1905` | Changing the column's number format accidentally set its **Data Type** to Date, so Power BI read the year as a date serial number | Reverted Data Type back to **Whole Number** with a plain number format |
| 9 | Country bar chart ranked by alphabetically-last artist names instead of top countries | Top N filter's "By value" field was set to **First Artist Name** instead of **Count of Artist Name** | Corrected the filter's aggregation to Count, re-applied |
| 10 | Country bar chart initially showed individual artist names instead of countries | Fields were placed on the wrong axes (Artist Name and Country of Origin swapped) | Rebuilt the field wells with Country of Origin on the category axis and Count of Artist Name as the value |
| 11 | Scatter chart (Debut Year vs Streams) collapsed into a single dot | No field in the Legend/Details well, so Power BI aggregated all 500 rows into one point | Added Artist Name to Details, restoring one point per artist |
| 12 | Scatter chart became unreadable — 500 artists each got a random legend color | Artist Name in the Legend field colors every distinct value separately, which doesn't scale to 500 categories | Ultimately replaced the scatter chart with a cleaner **Total Streams by Language** area chart, which told a similar story without per-point color clutter |

## Tools Used
Power BI Desktop · Power Query · DAX

## Files
- `spotify_dashboard.pbix` — full Power BI report
- `Most_Streamed_Artists_on_Spotify_17_07_2026_V1_1.csv` — source dataset
