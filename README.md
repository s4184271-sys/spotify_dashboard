# Most Streamed Artists on Spotify — Power BI Dashboard

An interactive Spotify-themed Power BI dashboard analyzing the **500 most streamed artists** in the dataset, with analysis of stream composition, artist type, genre, language, country, and debut year.

## Dataset

- **Source:** Kaggle — *Most Streamed Artists on Spotify*
- **Records:** 500 artists
- **Fields:** Artist Name, Sex, Country of Origin, Primary Genre, Primary Language, Artist Type, Debut Year, and stream measures

The analysis uses the dataset dated **17 July 2026**.

## Business Questions

- Which artists have the highest total streaming volume?
- What is the distribution between solo artists and groups?
- Which countries have the largest number of artists in the dataset?
- Which languages account for the most streams?
- Which genre has the highest artist count?
- How do lead streams compare with feature streams?
- Can users explore artists and segments interactively?

## Data Cleaning — Power Query

- Removed a hidden leading space from the `Artist Type` column name
- Checked for missing values and duplicate artist names
- Corrected `Debut Year` from an incorrect Date interpretation to Whole Number
- Corrected number formatting so years display as normal four-digit values

## Dashboard

### Slicers

1. Artist Name
2. Primary Genre
3. Artist Type
4. Country of Origin
5. Sex
6. Primary Language
7. Debut Year

### KPI Cards

- Total Streams
- Collaborative Streams
- Solo Streams
- Number of Artists
- Top Genre
- Lead Streams
- Feature Streams

### Visuals

- Top 10 Artists by Streams
- Solo vs Group distribution
- Top 10 Countries by Artist Count
- Total Streams by Language

## Key Findings

The dashboard shows the following results for the dataset:

- Solo artists represent **74.7%** of artists, compared with **25.3%** for groups.
- The United States has **264 artists**, compared with **55** for the UK in this dataset.
- **Hip-Hop** is the top genre by artist count, with **115 artists**.
- Lead streams total **6.07 billion**, while feature streams total **2.34 billion**.
- English-language streams are substantially higher than streams associated with the other languages in the dataset.

For individual artist values and rankings, the dashboard should be used as the source of the displayed figures.

## Key DAX Measure — Top Genre

The Top Genre measure ranks genres by artist count using `SUMMARIZE`, `TOPN`, and `CALCULATE`. It dynamically updates with the report's filter context.

## Problems Encountered & Solutions

| Problem | Solution |
|---|---|
| Hidden space in `Artist Type` column name | Renamed the column in Power Query |
| Incorrect card formatting | Adjusted the correct card value formatting settings |
| Unwanted card divider/icon | Disabled the relevant built-in visual formatting options |
| DAX table-name error | Used Power BI field autocomplete to confirm the exact table name |
| Top Genre returned the wrong category | Rebuilt the measure using frequency-based ranking |
| Lead/Feature cards showed duplicate values | Replaced the incorrect field in each visual |
| Stream values were double-scaled | Set display units appropriately |
| Debut Year appeared as dates | Changed the data type back to Whole Number |
| Country ranking used the wrong aggregation | Changed the Top N value to artist count |
| Country visual showed individual artists | Corrected the visual field placement |
| Scatter chart aggregated into one point | Added Artist Name to the appropriate detail field |
| Scatter chart became visually cluttered | Replaced it with Total Streams by Language |

## Tools

**Power BI Desktop · Power Query · DAX**

## Files

- `spotify_dashboard.pbix` — Power BI report

The source dataset is from Kaggle and is not listed as a repository file here.
