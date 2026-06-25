# Architecture Notes — Jal Rakshak

This is a quick writeup of how the prototype is put together, mostly so I (or anyone reviewing this) can remember the reasoning later.

## Why a single HTML file

Everything — markup, styles, and logic — lives in one `index.html`. For a hackathon prototype this made sense: no build step, no server, anyone can open it by double-clicking. The tradeoff is the file is long (~970 lines), but for something at this stage that felt like the right call over setting up a proper frontend framework.

## Rough structure

```
Login screen
  └─ name + role (Citizen / Government Official) + region selector
        │
        ├── Citizen view
        │     └─ report form (location, observations, optional photo/description)
        │           → submission increments a "citizen reports" count on that station
        │
        └── Official view
              ├─ Monitoring dashboard (4 stations)
              │     ├─ simulated sensor readings per station
              │     ├─ "Run Detection Scan" → classifies each station
              │     └─ spread prediction for any contaminated station
              ├─ Manual lab data entry (pH, turbidity, DO, coliform, BOD) per station
              ├─ Recommended action severity panel
              └─ Alert list with "mark action taken" tracking
```

It's all client-side. No backend, no database — role state and citizen reports live in JavaScript variables for the session, which is fine for a demo but wouldn't survive a page refresh in its current form.

## The four monitoring stations

I picked these because they map to a believable upstream-to-downstream stretch of the Narmada near Jabalpur:

| Station | Type | Rough distance downstream |
|---|---|---|
| Gaurighat | Upstream residential | 0 km (reference point) |
| Tilwaraghat | City bathing ghat | ~3.5 km |
| City Drain Outlet | Major discharge point | ~5.2 km |
| Bhedaghat | Downstream tourist area | ~12.8 km |

Each station has four simulated readings: discoloration %, foam coverage %, turbidity spike %, and flow rate (m/s). These stand in for what satellite imagery (discoloration, foam) and physical river sensors (flow, turbidity) would actually report in a real deployment — there's no live data source behind them right now, and the app says so.

## How the detection logic works

For each station, the readings get compared against rough thresholds for discoloration, foam, and turbidity. Crossing more thresholds, or crossing them by more, pushes the station from Clean → Moderate → High → Critical. This is plain rule-based logic — no model, no API call — which was a deliberate choice after I ran into cost/access issues trying to wire in an LLM. It also means the result is fully explainable: you can look at the numbers and the thresholds and see exactly why a station got flagged.

## How the spread prediction works

For any station that gets flagged, I find the next station downstream, take the distance between them, and divide by the current flow rate to get a rough travel time. E.g. distance 7.6 km at 2.3 m/s works out to roughly 55 minutes. It's a simple physics estimate, not a hydrology model — no accounting for weather, seasonal flow changes, or river geometry. That's a known simplification, not an oversight.

## Alerts

If a station is flagged Moderate or worse, the system writes a message addressed to MPCB / Jabalpur Municipal Corporation, including the station, the specific readings that triggered the flag, and the spread prediction if there is one. Officials can mark these as "action taken" with a short note on what was done — mostly to make the dashboard feel like a working tool rather than a one-way alert feed.

## What's not real here

Worth being upfront about, since this matters for how the project should be judged:

- Sensor readings are simulated, not live
- There's no real authentication — the login screen just lets you pick a name and role to demo the two views
- Citizen reports and "action taken" notes aren't saved anywhere persistent — refreshing the page resets the session
- The spread-time math is a basic distance/flow-rate calculation, not a real contamination dispersion model

## If this went further

The realistic next step would be replacing the simulated readings with something like Sentinel-2 satellite imagery for the visual indicators, and actual IoT sensors for flow/turbidity, while keeping the same detection and alerting logic on top. The rule-based core doesn't actually need to change for that — it just needs real numbers to evaluate.

## Built with

Architected and coded using IBM BOB, across a few rounds — the first version was a manual water-testing calculator that didn't actually match what I wanted, so a chunk of this was rebuilt once I was clearer on the actual concept (a monitoring/alert system, not a calculator).
