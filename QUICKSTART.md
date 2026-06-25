# Demo Guide — Jal Rakshak

Notes for running through the prototype, mostly for myself before presenting, but useful if anyone else needs to try it.

## Opening it

Just double-click `index.html`, or open it with any browser. No API key, no install, no internet needed — everything runs locally in the browser.

## Walking through it

**1. Login screen**
Pick a region (India is the only active one right now, others show as "Coming Soon" — that's intentional, signals this is meant to scale beyond one river eventually). Enter any name, then choose a role: Citizen or Government Official.

**2. Citizen view**
Pick a station from the dropdown (or "Other"), check off whatever's relevant — discoloration, foam, smell, dead fish — add a short note if you want, and submit. You'll see a confirmation, and that station's "citizen reports" count goes up on the official dashboard. This part has no real photo processing — the upload field is there for the interface, not functional yet.

**3. Switch to Official view**
Log out (or just open the role switch) and come back in as Government Official. This is where most of the actual system lives:
- Four monitoring stations, each showing simulated sensor readings
- Click "Run Detection Scan" — it checks all four stations and classifies each as Clean / Moderate / High / Critical
- For anything flagged, it shows a rough prediction of when contamination would reach the next station downstream
- You can manually enter lab readings (pH, turbidity, DO, coliform, BOD) for a station if you have real data to override the simulated values
- Each flagged station generates an alert message addressed to MPCB / Jabalpur Municipal Corporation, and you can mark it "action taken" with a short note

## If something doesn't work

- **Nothing happens on scan** — check the browser console (F12) for errors, and make sure you're not opening the file through some restrictive viewer; a normal browser tab should work fine.
- **Citizen report doesn't show up on dashboard** — this only persists for the current browser session; refreshing the page resets everything, since there's no real backend.
- **Login doesn't "remember" you** — that's expected. This is a demo-level login, not real authentication.

## Things worth saying out loud during a demo

- This is piloted on the Narmada near Jabalpur, but the architecture isn't tied to one river — the region selector and the underlying logic are built to extend elsewhere.
- The sensor readings are simulated. I say this upfront rather than letting someone assume it's live satellite data — the detection and prediction logic itself is real and would work the same way with real input.
- The risk classification is a transparent rule-based system, not a black-box model — I made that choice deliberately after running into cost and access issues trying to wire in an LLM, and it has the side benefit of being fully explainable.

## Rough parameter reference

For the manual lab-data entry in the Official view:

| Parameter | Safe range | Where it starts becoming a problem |
|---|---|---|
| pH | 6.5–8.5 | Below 6.5 or above 8.5 |
| Turbidity | < 5 NTU | Above 10 NTU |
| Dissolved Oxygen | > 6 mg/L | Below 4 mg/L |
| Fecal Coliform | < 500 MPN/100ml | Above 2000 |
| BOD | < 3 mg/L | Above 6 mg/L |

## What I'd say if someone pushes on the technical side

**"Is this connected to real sensors?"**
No — right now it's simulated data, clearly labeled as such in the app. The detection and spread-prediction logic is real; it just doesn't have a live data source behind it yet.

**"Why rule-based instead of an actual AI model?"**
I originally tried wiring this to an LLM (first OpenAI, then Claude), but ran into API cost and credit issues that weren't sustainable for a student project. Rather than fake it or quietly skip the problem, I switched to a transparent rule-based engine — which also means the reasoning is fully explainable, which matters for the Responsible AI side of this.

**"How accurate is the spread prediction?"**
It's a basic distance-divided-by-flow-rate calculation, not a hydrology model. It's meant to demonstrate the concept of an early-warning timeline, not give scientifically precise predictions.

**"What would it take to make this real?"**
Real satellite imagery (something like Sentinel-2) for the visual indicators, actual IoT water sensors for flow and turbidity, and a backend to persist citizen reports and official actions instead of resetting on refresh.

## Before presenting

- [ ] Open `index.html` once beforehand to make sure it still loads cleanly
- [ ] Know which station you'll demo as "Critical" (City Drain Outlet usually shows the clearest result)
- [ ] Be ready to say plainly what's simulated vs. real — that's a strength, not something to hide
- [ ] Have the GitHub repo and live link ready to share
