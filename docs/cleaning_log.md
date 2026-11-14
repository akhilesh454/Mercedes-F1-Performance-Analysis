# Cleaning Log

| # | Found | Action | Why |
|---|---|---|---|
| 1 | Dataset covers 1950–2024, all teams | Filtered races to 2011–2024 (286), results/qualifying/sprints to constructorId 131, merged on raceId (Inner) | Scope the project; ID 131 also covers 1954–55 and 2010 |
| 2 | pit_stops, lap_times, driver_standings have no team ID | Merged with Mercedes results on raceId + driverId | Excludes Hamilton's 2011–2012 McLaren data; raceId-only merge gave ~22,742 wrong rows |
| 3 | Missing values stored as text `\N` | Replaced with null before setting types | Number columns failed to convert |
| 4 | Remaining nulls (position, q2, q3, fastest lap) | Kept | They mean DNF / knocked out / no timed lap |
| 5 | Pit `duration` mixed `24.5` and `18:56.516` (54 Mercedes stops) | Pit Time (sec) = milliseconds ÷ 1000; removed duration | Avoid deleting real stops (1,110 kept, not 1,056) |
| 6 | Average pit time 89 s (median 23 s) | Stop Type: Normal ≤ 60 s (1,056), Long Stop ≤ 600 s (5), Red Flag (49) | Red-flag stoppages distort averages |
| 7 | q1–q3 stored as m:ss.sss text; empty-text values in q3 | Fixed empty text → null; converted to Q1/Q2/Q3 (sec) | Needed for teammate gap calculations |
| 8 | 26 status labels | Status Group + Result Type conditional columns | Readable reliability analysis |
| 9 | Judgement calls | Puncture = Accident/Incident; Wheel nut, Rear wing = Mechanical; Retired kept as Unknown | Documented, not guessed |
| 10 | Renamed events | Brazilian → São Paulo, Mexican → Mexico City, European (Baku 2016) → Azerbaijan | One event = one name |
| 11 | Same name, different tracks (German GP, European GP) | Circuit analysis uses circuit name | Avoid mixing tracks |
| 12 | No regulation column | Built Dim_Season (V8 / Hybrid / Wide Car / Ground Effect) with Era Order | Core business question |
| 13 | Schumacher, Hungary 2012: "+11 Laps" with no position | Excluded rows without position from positions gained (0.61 → 0.57) | Source-data inconsistency |
| 14 | Lapped finishes counted as DNF (79 vs 55) | Fixed exact label match in Result Type rule | Validated against known totals |
| 15 | Unused data | Removed url, car number, mixed time text columns; deleted constructor_results and seasons | Lean model |
