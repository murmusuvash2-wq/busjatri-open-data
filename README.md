# West Bengal Bus Open Data — BusJatri

An open dataset of **bus routes, timetables, stoppages and stops across West Bengal, India**.

This is the dataset behind **[BusJatri](https://busjatri.in)** — a free, bilingual (English / Bengali) bus
timetable for West Bengal covering SBSTC, WBTC, CSTC, NBSTC and hundreds of private operators.

**Website: [busjatri.in](https://busjatri.in)**

---

## What's inside

| File | Rows | What it is |
|---|---|---|
| `data/buses.csv` | 4,728 | One row per bus service — operator, bus type, origin, destination, departure/arrival time, depot, registration no., data source |
| `data/routes.csv` | 2,925 | One row per origin → destination pair — bus count, operators, first & last departure, via-stoppages |
| `data/stoppages.csv` | 61,126 | Ordered stoppage list per bus (route → stop sequence, with up/down times where known) |
| `data/stops.csv` | 2,740 | Every stop name, with latitude/longitude where known |

### Column reference

**`buses.csv`**

| Column | Meaning |
|---|---|
| `bus_id` | Stable internal id for the service |
| `bus_name` | Service / bus name as it appears on the board |
| `operator` | Operating company or corporation |
| `bus_type` | e.g. `Government - NON AC`, `AC Volvo`, `Private` |
| `reg_no` | Registration number, where known |
| `depot` | Depot the service runs from |
| `origin` / `destination` | Route endpoints |
| `departure_time` / `arrival_time` | Scheduled times. **Blank = no published time** (see caveats) |
| `fare` | Fare, where officially published |
| `total_stoppages` | Number of stops on the route |
| `source` | Where this row came from (official PDF, depot board, commuter report, …) |
| `detail_url` | Link to the full timetable page on busjatri.in |

**`routes.csv`** — `via_stoppages` is the intermediate stop chain (`A > B > C`) for the longest-stop
variant of the route, useful for building stop-to-stop search.

---

## Coverage

- **Government:** SBSTC (South Bengal), WBTC + CSTC (Kolkata and suburbs), NBSTC (North Bengal)
- **Private:** hundreds of operators running express, ordinary and AC services
- **Long-distance:** Kolkata ↔ Digha, Kolkata ↔ Asansol, Siliguri ↔ Cooch Behar, Durgapur ↔ Kolkata, …
- **Kolkata city & suburban:** route numbers, stoppage chains and operator (no fixed times — see below)

---

## Where the data comes from

1. **Official schedules** — WBTC, SBSTC, NBSTC and West Bengal Transport Department published route
   lists and timetable PDFs.
2. **Depot and bus-stand boards** — departure boards at depots such as Bankura SBSTC and Cooch Behar NBSTC.
3. **Commuter reports** — corrections submitted by passengers.
4. **Other public timetable sources** — cross-checked against the above.

Every timetable page on the site shows the date it was last refreshed. Times that cannot be verified
are left blank rather than estimated.

---

## Important caveats

- **City and suburban buses in Kolkata run to frequency.** They have no published departure times,
  so `departure_time` is blank for them. Blank does **not** mean missing — it means no fixed time exists.
  Do not read a blank as "00:00".
- **Schedules change.** Operators alter timings without notice. Always confirm with the operator,
  conductor or bus stand before relying on a departure time.
- **This is a travel guide, not an official real-time feed.** It is not affiliated with any transport
  corporation.
- Coverage is broad but not exhaustive. Missing routes are added as sources surface.

---

## Usage

```python
import pandas as pd

buses  = pd.read_csv("data/buses.csv")
routes = pd.read_csv("data/routes.csv")

# Longest routes with a published departure time
timed = buses[buses["departure_time"].notna()]
print(timed.sort_values("total_stoppages", ascending=False).head(10))

# Every operator running Durgapur -> Kolkata
print(routes.query("origin == 'Durgapur' and destination == 'Kolkata'")["operators"].iloc[0])
```

---

## Contributing

Found a wrong time, a missing route, or a stop that does not exist? Please open an issue, or use the
**Report Time** button on any timetable page at [busjatri.in](https://busjatri.in).

---

## License

Data is released under **CC BY 4.0** — use it, remix it, build on it, with attribution to
**[BusJatri](https://busjatri.in)**.

---

## Links

- **BusJatri — West Bengal bus timetable:** https://busjatri.in
- **All routes:** https://busjatri.in/bus-time-table/
- **Kolkata city bus:** https://busjatri.in/kolkata-city-bus-timetable
- **Contact:** busjatri@zohomail.in
