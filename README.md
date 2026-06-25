# Jal Rakshak (जल रक्षक)

A prototype I built for the 1M1B AI for Sustainability internship. It monitors sewage discharge along the Narmada River near Jabalpur, Madhya Pradesh, and predicts how contamination spreads downstream before it reaches the next town.

## Why I built this

I grew up in Jabalpur. The Narmada was where my family went for festivals and prayers. Over the years I watched the water change color, saw foam collecting at the ghats, and heard older relatives say the river wasn't what it used to be.

Jabalpur is officially ranked the biggest polluter of the Narmada by the Madhya Pradesh Pollution Control Board — about 136 million litres of sewage a day. In 2026 the MP High Court issued notice over roughly 98 million litres of that being untreated. There's no real-time system that tells you where the discharge is happening or how long you have before it reaches the next stretch of river. That's the gap this tries to fill.

## What it actually does

The dashboard simulates four monitoring points along the river — Gaurighat, Tilwaraghat, the main city drain outlet, and Bhedaghat downstream. Each one has readings for discoloration, foam coverage, turbidity, and flow rate, standing in for what real satellite imagery or river sensors would report.

Click "Run Detection Scan" and it checks all four stations, flags which ones look contaminated, and — if a station is bad — calculates roughly how long it'll take that contamination to reach the next station downstream, based on distance and flow rate. It then writes a formal alert addressed to the Pollution Control Board and Jabalpur Municipal Corporation.

There are two ways to use it:
- **Citizen view** — anyone can report what they're seeing at a location (foam, smell, dead fish, etc.), which shows up as a report count on that station.
- **Official view** — full dashboard, plus the ability to enter real lab readings (pH, turbidity, dissolved oxygen, coliform, BOD) for a station, see a recommended response level, and mark an alert as actioned.

## Important — what's real and what's simulated

The sensor readings right now are simulated. I don't have access to actual satellite feeds or river sensors, so the numbers are designed to represent realistic scenarios rather than live data. This is clearly labeled in the app itself. The detection logic (the thresholds, the risk classification, the spread-time math) is real and would work the same way if real sensor data were plugged in — that's the part of the system I actually built and tested.

If this were to go further, the next real step would be connecting it to something like Sentinel-2 satellite imagery for the discoloration/foam detection, and actual IoT water sensors for flow and turbidity data.

## How it works, roughly

Each station gets scored against rough safety thresholds for discoloration, foam, and turbidity. Cross a few thresholds and the station gets flagged Moderate, High, or Critical. For any flagged station, I calculate the distance to the next station downstream and divide by the current flow rate to get a rough travel time — e.g. "contamination reaches Bhedaghat in about 14 hours." That number then goes into the auto-generated alert.

## Responsible AI — the actual considerations, not just a checklist

- **Transparency:** the app says outright when data is simulated, and the detection logic is rule-based, not a black box — anyone could read the thresholds and see why a station got flagged.
- **Fairness:** all four stations (residential, tourist, industrial) get monitored the same way, not just the ones near "important" areas.
- **Privacy:** citizen reports don't require any personal data beyond a name, and nothing is stored anywhere outside the browser session.
- **Ethics:** the system is meant to support a human decision-maker (the Pollution Board), not replace one. It flags and recommends — it doesn't take action on its own.

## Built with

This was built using IBM BOB — I went through it iteratively: first asking it to help me architect the system, then to build it, then rebuilding a chunk of it when I realized my first version (a manual water-testing calculator) didn't actually match what I was trying to do. That rebuild was honestly one of the more useful parts of the process — it forced me to be specific about what I actually wanted instead of accepting the first working thing.

## Limitations, honestly

- Sensor data is simulated, not live
- Only four stations, all on one river
- Spread prediction is a simple distance/flow-rate calculation, not real hydrology modeling
- Login is for demo purposes only — no real authentication or data storage

## If I kept working on this

Connect to real satellite imagery and IoT sensors, add more monitoring points, extend the spread model to account for weather and seasonal flow changes, and eventually scale the same architecture to other rivers — not just the Narmada.

---

Built for the 1M1B AI for Sustainability Virtual Internship, in collaboration with IBM SkillsBuild and AICTE.
