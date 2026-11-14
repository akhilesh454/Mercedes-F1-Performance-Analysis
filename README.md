# Mercedes-AMG F1 Performance Analysis (Power BI)

An 8-page interactive Power BI dashboard analysing the Mercedes Formula 1 team from **2011 to 2024**: how it rose from midfield, why it dominated for eight seasons, and why it declined.

> **The Big Picture:** Mercedes' story is a regulation story. It mastered the 2014 hybrid rules first, turned qualifying pace into 8 straight constructors' titles and 120 wins, then lost its edge when the 2022 rules reset the field, falling from 1st to 4th by 2024.

---

## Project at a glance

| | |
|---|---|
| **Scope** | Mercedes team only (constructorId 131), seasons 2011–2024 |
| **Data** | Ergast F1 dataset, 14 relational CSV files (1950–2024) |
| **Size in scope** | 286 races · 572 race starts · 5 drivers · 35 circuits · 1,110 pit stops · 32,354 laps |
| **Tools** | Power Query · Power BI data model · DAX · Power BI report design |
| **Pages** | Home · Overview · Seasons · Drivers · Circuits · Qualifying · Reliability · Insights |

### Headline numbers

| Constructors' titles | Drivers' titles | Wins | Podiums | Poles | Fastest laps | Points | Win rate | Finish rate |
|---|---|---|---|---|---|---|---|---|
| 8 | 7 | 120 | 278 | 135 | 99 | 7,476.5 | 42.0% | 90.0% |

---

## Business questions

| Page | Question |
|---|---|
| Overview | How successful was Mercedes from 2011 to 2024? |
| Seasons | How did rule changes shape Mercedes' performance? |
| Drivers | How did each driver perform, and who beat whom? |
| Circuits | Where was Mercedes strongest, and where did it struggle? |
| Qualifying | Was Mercedes stronger on Saturday or on Sunday? |
| Reliability | How often did Mercedes fail to finish, and how did its pit stops compare? |
| Insights | What should a decision-maker take away from 14 seasons? |

---

## Process

### 1. Extraction (Power Query)
- Only `races` holds the year, so it was filtered to 2011–2024 first (286 rows); other tables were cut down with **Inner merges** on `raceId`.
- `results`, `qualifying` and `sprint_results` filtered to `constructorId = 131` (ID 131 also covers 1954–55 and 2010, so the year merge was still required).
- `pit_stops`, `lap_times` and `driver_standings` have no team column: matched to Mercedes results on **raceId + driverId**, which excludes Hamilton's 2011–2012 McLaren races.
- `constructor_standings` kept for **all teams** to compare Mercedes with Red Bull, Ferrari and McLaren.
- Dimension tables reduced with helper lists (Reference → Remove Duplicates → Inner merge): 5 drivers, 35 circuits, 23 teams, 26 status labels.

### 2. Cleaning and transformation
| Issue | Action |
|---|---|
| Missing values stored as the text `\N` | Replaced with null; remaining blanks are meaningful (DNF, knocked out in qualifying) |
| Pit `duration` mixed `24.5` and `18:56.516` | Created **Pit Time (sec)** from `milliseconds`; no rows deleted |
| 49 red-flag stoppages inflated average pit time to 89 s | Added **Stop Type** (Normal / Long Stop / Red Flag); averages use normal stops (23.7 s) |
| Qualifying times stored as text (`1:26.572`) | Converted to **Q1/Q2/Q3 (sec)** |
| 26 different finish-status labels | Grouped into **Status Group** and **Result Type** (Finished / DNF / DSQ) |
| Renamed events (Brazilian → São Paulo, Mexican → Mexico City, European 2016 → Azerbaijan) | Standardised **Grand Prix** column |
| No regulation information in the data | Built **Dim_Season** (V8, Hybrid, Wide Car, Ground Effect eras) |
| Source inconsistency: Schumacher, Hungary 2012, "+11 Laps" with no position | Excluded rows without a position from positions-gained |

Full cleaning log: [`docs/cleaning_log.md`](docs/cleaning_log.md)

### 3. Data model
Star schema with **19 one-to-many, single-direction relationships**.

- **Facts:** results, qualifying, sprint_results, pit_stops, lap_times, driver_standings, constructor_standings
- **Dimensions:** races, drivers, circuits, constructors, status, Dim_Season, Date
- Time filter chain: `Dim_Season → Date → races → facts`, so one Season slicer controls every page.
- `constructors` connects only to `constructor_standings` so a team selection never blanks Mercedes-only visuals.

### 4. Measures
Simple counts and ratios built with **Quick measures**; complex logic in **DAX**, including:
- **Constructors' / Drivers' Titles:** position 1 at the last round of each season (SUMX over seasons so totals match rows)
- **1-2 Finishes** and **Front-Row Lockouts:** race-by-race check that both cars took 1st and 2nd
- **Pole-to-Win %:** pole-sitter won the same race (LOOKUPVALUE)
- **Teammate Qualifying Gap:** last session both drivers set a time (Q3, else Q2, else Q1)
- **Win %** at two levels: per race (team) and per race start (driver)

KPI definitions: [`docs/kpi_definitions.md`](docs/kpi_definitions.md)

---

## Key insights

1. **Rules made the difference.** Win rate went from **7%** (V8 era) to **86%** (Hybrid era), 59% (Wide Car era), then back to **7%** under the 2022 ground-effect rules.
2. **Eight straight titles.** Every constructors' championship from 2014 to 2021, plus 7 drivers' titles (Hamilton 6, Rosberg 1).
3. **Saturday decided Sunday.** **114 of 120 wins** came from the top three grid slots; 73 from pole.
4. **Hamilton led the team** with 84 wins (70%); Rosberg beat him to the 2016 title by 5 points.
5. **Reliable, not flawless.** 90% finish rate; collisions (16) caused more DNFs than any mechanical failure.
6. **Winners almost everywhere.** Won at 30 of 35 circuits, including 8 of 8 at Sochi; Sepang was the toughest (14%).

### Recommendations
1. **Protect qualifying performance:** grid position was the strongest predictor of results.
2. **Treat rule changes as the biggest risk and opportunity:** early mastery of 2014 brought 8 years at the top; a weak start in 2022 cost three.
3. **Reduce incident-related retirements:** mechanical reliability was strong; collisions and accidents caused almost half of all DNFs.

---

## Limitations
- No tyre, weather, telemetry or budget data: the dashboard shows *what* happened, not the engineering reasons *why*.
- Pit time is total pit-lane time, not stationary time.
- Small reliability sample (55 DNFs in 14 seasons).
- Ergast stopped after 2024, so the data cannot be refreshed from the same source.

## Future improvements
- Add 2018+ tyre and weather data with FastF1
- Race-pace page from lap times, median teammate gap, race drill-through
- Repeat the model for Red Bull or Ferrari to compare dominance periods

---

## Repository structure
```
├── README.md
├── Mercedes_F1_Dashboard.pbix
├── dataset/
│   ├── *.csv            Ergast F1 CSV files
│   └── README_data.md   source and file descriptions
└── docs/
    ├── cleaning_log.md
    └── kpi_definitions.md
```

## How to open
1. Install [Power BI Desktop](https://powerbi.microsoft.com/desktop/) (free, Windows).
2. Download `Mercedes_F1_Dashboard.pbix` and open it.
3. Use the sidebar to move between pages and the Season / Era / Driver slicers to filter.

## Data source and credits
- Data: Ergast Developer API Formula 1 dataset (final release, through the 2024 season).
- Cover image: Lewis Hamilton's final race for Mercedes, Abu Dhabi 2024; images belong to their respective owners.
- *Personal portfolio project. Not affiliated with the Mercedes-AMG Petronas F1 Team.*

## Author
**Akhilesh Pradhan** · Data Analyst / Business Analyst
LinkedIn: _[add link]_ · Email: _[add email]_
