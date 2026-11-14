# Dataset

**Source:** Ergast Developer API – Formula 1 dataset, final release (seasons 1950–2024).
Missing values are stored as the text `\N`. Tables link through ID columns.

The project uses Mercedes (constructorId 131), seasons 2011–2024; filtering is done in Power Query, so the CSV files here are the original, unfiltered files.

| File | One row represents | Rows | Used in project |
|---|---|---|---|
| races.csv | One Grand Prix (year, round, circuit, date) | 1,125 | Yes – dimension (286 after filter) |
| results.csv | One driver's result in one race | 26,759 | Yes – fact (572) |
| qualifying.csv | One driver's qualifying session (Q1/Q2/Q3 times) | 10,494 | Yes – fact (571) |
| sprint_results.csv | One driver's sprint result (2021+) | 360 | Yes – fact (36) |
| pit_stops.csv | One pit stop (2011+) | 11,371 | Yes – fact (1,110) |
| lap_times.csv | One lap by one driver in one race | 589,081 | Yes – fact (32,354) |
| driver_standings.csv | Driver championship position after each race | 34,863 | Yes – fact (572) |
| constructor_standings.csv | Team championship position after each race | 13,391 | Yes – fact, all teams (2,993) |
| drivers.csv | One driver | 861 | Yes – dimension (5) |
| constructors.csv | One team | 212 | Yes – dimension (23) |
| circuits.csv | One track with latitude/longitude | 77 | Yes – dimension (35) |
| status.csv | One finish-status label | 139 | Yes – dimension (26) |
| constructor_results.csv | Team points per race | 12,625 | No |
| seasons.csv | One year with a Wikipedia link | 75 | No |

**Note:** `lap_times.csv` is the largest file (about 17.6 MB); all files fit within GitHub's 25 MB web-upload limit.
