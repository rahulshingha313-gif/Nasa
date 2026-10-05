# Flame in Freefall

**AI-assisted exploration of NASA microgravity combustion research for spacecraft fire safety**

A prototype built for the 2026 NASA Space Apps Challenge: *Flame in Freefall: AI-Powered Fire Safety Insights from Microgravity Combustion Data*.

- **Live demo:** https://nasa-five-rose.vercel.app/
- **Official challenge page:** https://www.spaceappschallenge.org/2026/challenges/flame-in-freefall-ai-powered-fire-safety-insights-from-microgravity-combustion-data/

---

## Table of Contents

1. [Overview](#overview)
2. [Why This Matters](#why-this-matters)
3. [Features](#features)
4. [The Dataset](#the-dataset)
5. [How Flame AI Works](#how-flame-ai-works)
6. [Data Integrity Policy](#data-integrity-policy)
7. [Technology Stack](#technology-stack)
8. [Project Structure](#project-structure)
9. [Run Locally](#run-locally)
10. [Build and Deploy](#build-and-deploy)
11. [Extending the Project](#extending-the-project)
12. [Limitations](#limitations)
13. [NASA Sources](#nasa-sources)
14. [Disclaimer](#disclaimer)

---

## Overview

On Earth, hot gas rises because of buoyancy, so flames are teardrop-shaped and flicker. On the International Space Station (ISS), buoyancy is almost absent, and flames behave very differently: they can become rounded, dim, and unusually stable, yet they may also respond sharply to small changes in airflow or oxygen.

NASA has run several combustion experiments on the ISS to understand these differences. Flame in Freefall turns a small, carefully verified selection of that research into an interactive web application. Users can explore the experiments, compare them side by side, ask questions, review fire-safety insights, and try a conceptual flame visualizer.

The application is a **fully static frontend**. It needs no backend, no database, no paid API, and no API key.

## Why This Matters

Fire is one of the most serious hazards for a crewed spacecraft. Crews cannot leave, ventilation is limited, and materials must be screened for flammability. Understanding how materials ignite, spread flame, and extinguish in microgravity supports:

- Spacecraft materials flammability screening
- Design of fire detection and suppression systems
- Computational combustion models used in safety design
- Planning for future long-duration missions

## Features

The site has seven sections.

| Section | What it does |
|---|---|
| **Home** | Animated microgravity flame, challenge summary, three headline statistics drawn from NASA sources |
| **Explore Data** | Searchable, filterable experiment explorer (filters: experiment, fuel, gravity, oxygen, airflow, flame behavior). Each card lists fuel, gravity, oxygen, airflow, flame behavior, extinction behavior, safety relevance, and source links |
| **Compare** | Select two or three experiments and compare objective, fuel, oxygen, airflow, geometry, flame behavior, extinction, and fire-safety relevance. Includes a bar chart of maximum airflow tested |
| **Flame AI** | Local, rule-based research assistant. Answers are structured as Key Finding, Evidence, Related Experiment, NASA Source, and Limitation |
| **Fire Safety** | Eight insight cards: flame growth, flame stability, extinction, oxygen effect, airflow effect, fuel effect, spacecraft fire safety, Moon/Mars relevance |
| **Simulator** | Conceptual flame visualizer with Earth and Microgravity modes and oxygen, airflow, and gravity controls |
| **NASA Sources** | Library of the NASA pages and technical reports used to build the dataset |

### Visual design and motion

- Dark space theme with orange and amber flame glow and blue-white scientific accents
- Glass panels with glowing borders
- Animated starfield (Canvas), floating particles, flame glow, card hover effects, page transitions, and animated chart bars
- Responsive layout for desktop, tablet, and mobile
- All animation is disabled when the user's system requests reduced motion (`prefers-reduced-motion`)

## The Dataset

The dataset lives in `src/data.js` and is deliberately small. Every entry is drawn from NASA pages or NASA Technical Reports Server (NTRS) documents, and each entry links back to its sources.

### Experiments included

**BASS (Burning and Suppression of Solids)**
- Examined how a variety of solid fuels burn and can be extinguished in microgravity.
- Samples: thin and thick flat samples, acrylic spheres, and candles, mounted in a small wind tunnel with airflow up to 40 cm/s.
- Flames were highly sensitive to airflow in the 0 to 5 cm/s range. Below 1 cm/s, flames became dim blue and very stable.
- Some tests used a nitrogen jet to try to extinguish flames. The smallest flames on acrylic slabs burned for more than 5 minutes before self-extinguishing.
- NASA reports analysis of 59 BASS burn tests.

**BASS-II**
- Tested the hypothesis that, with adequate ventilation, materials burn as well as or better in microgravity than in normal gravity under otherwise identical conditions.
- Samples: thin and thick flat samples, fabrics, acrylic slabs, spheres, and cylinders, with airflow up to 53 cm/s, run in the Microgravity Science Glovebox.
- Flames could be sustained at very low flow, where they are dim blue and stable, but could flare up quickly if airflow suddenly increased.
- A 2014 ISS status report describes a fabric sample that self-extinguished at a lower oxygen level.
- Including earlier BASS results, well over one hundred tests were conducted.

**ACME (Advanced Combustion via Microgravity Experiments)**
- Six independent studies of non-premixed gaseous-fuel flames, run in the Combustion Integrated Rack (CIR). In-orbit testing began in 2017 and ended in February 2022.
- Over 1,500 flames were ignited, more than three times the number originally planned.
- Notable results: non-premixed cool flames of gaseous fuels without the enhancements required in ground testing, quasi-steady spherical non-premixed flames for the first time, radiative heat loss leading to extinction of larger spherical flames, and electric-field effects with potential for reducing emissions.

**FLEX and FLEX-2 (FLame EXtinguishment Experiments)**
- FLEX studied the effectiveness of fire suppressants on burning fuel droplets (such as heptane and methanol) in the CIR.
- It led to the discovery of cool flames, in which fuel continued to react after the visible flame was extinguished. Cool flames produce carbon monoxide and formaldehyde rather than the carbon dioxide and water of typical flames.
- FLEX-2 examined droplet burn rates, the conditions required for soot formation, and how mixtures of liquid fuels evaporate before burning.

### Data fields

```js
{
  id, experiment, full, objective,
  fuel, fuelType, gravity,
  oxygen, o2, airflow, flow, maxFlow, geometry,
  flameBehavior, tags, extinctionBehavior,
  safetyRelevance, src   // src = list of source keys
}
```

Where a value could not be verified from a NASA source, the field reads **"Data unavailable"** instead of a guess.

## How Flame AI Works

Flame AI is a **local, rule-based assistant**. It does not call any external AI service.

1. The user types a question or clicks one of the example questions.
2. The question is lowercased and matched against keyword lists in the knowledge base (`KB` in `src/data.js`).
3. The entry with the most keyword hits is selected. If nothing matches, the assistant states that no verified answer exists in the local dataset.
4. The answer is shown in a fixed structure: **Key Finding**, **Evidence**, **Related Experiment**, **NASA Source**, **Limitation**.

The interface is labeled **"AI-assisted project interpretation"**. It is a project feature that presents interpretations of the verified dataset. It is not an official NASA system, and it does not generate new scientific claims.

Example questions it can answer:

- How does fire behave in microgravity?
- Compare BASS and BASS-II.
- What affects flame extinction?
- Which experiment is relevant to spacecraft fire safety?
- What can this teach us about future Mars habitats?

## Data Integrity Policy

- No NASA data is invented. Unverified values read "Data unavailable".
- Every important claim carries a link to a NASA source.
- Project-derived visuals are labeled as such. For example, the airflow bar chart is drawn by this project from values reported by NASA, and it shows "Data unavailable" for experiments without a verified airflow figure.
- The Simulator is explicitly labeled as a conceptual, educational visualization, not a combustion physics simulation.
- Moon and Mars relevance is presented as an open question, because the ISS experiments covered here were conducted in microgravity, not in lunar or Martian partial gravity.

## Technology Stack

| Layer | Choice |
|---|---|
| Framework | React 18 |
| Build tool | Vite 5 |
| Styling | Plain CSS |
| Graphics | SVG (flame, charts) and Canvas (starfield) |
| Data | Local JavaScript module |
| Hosting | Any static host (currently Vercel) |

Dependencies are intentionally minimal: `react`, `react-dom`, `vite`, and `@vitejs/plugin-react`. There are no charting, animation, or UI libraries.

## Project Structure

```
flame/
  index.html          Entry HTML
  package.json        Scripts and dependencies
  vite.config.js      Vite config (base './' for static hosting)
  README.md           This file
  src/
    main.jsx          React entry point
    App.jsx           All pages and components
    data.js           Experiments, sources, AI knowledge base, insights
    styles.css        Theme, layout, animations, responsive rules
```

## Run Locally

Requirements: Node.js 18 or newer (LTS recommended).

```bash
npm install
npm run dev
```

Open the local address shown in the terminal (usually `http://localhost:5173`).

Note: the project must be run through Vite. Opening `index.html` directly, or through a plain file server such as VS Code Live Server, shows a blank page because the browser cannot run `.jsx` files.

## Build and Deploy

```bash
npm run build
```

This creates a `dist/` folder containing the static site. `vite.config.js` sets `base: './'`, so the output works from any path.

**Vercel:** import the repository, framework preset *Vite*, build command `npm run build`, output directory `dist`.

**GitHub Pages:** upload or publish the contents of `dist/` (not the project root).

## Extending the Project

- **Add an experiment:** append an object to the `EXP` array in `src/data.js`, using the fields above and only verified values. Add its sources to `SOURCES`.
- **Add an AI answer:** append an entry to `KB` with `k` (keywords), `key`, `ev`, `rel` (experiment id), `src` (source keys), and `lim`.
- **Add an insight card:** append a `[title, text, sourceKey]` row to `INS`.
- Possible future additions: ACME's six individual investigations as separate entries, SoFIE-GEL, microgravity versus normal-gravity test comparisons, and richer charts from NASA's Physical Sciences Informatics data.

## Limitations

- The dataset is a small curated subset, not a complete record of NASA combustion research.
- Several quantitative values (for example exact oxygen concentrations per test, extinction thresholds, and Moon/Mars-specific values) are marked "Data unavailable" because they were not verified.
- Flame AI matches keywords. It does not understand free-form language and cannot answer questions outside its knowledge base.
- The Simulator is illustrative only and does not model real combustion physics.
- Findings from ISS microgravity experiments should not be assumed to transfer directly to partial-gravity environments such as the Moon or Mars.

## NASA Sources

| Source | Link |
|---|---|
| NASA Glenn: BASS-II (Microgravity Science Glovebox) | https://www1.grc.nasa.gov/space/iss-research/msg/bass-2/ |
| NTRS 20160000593: Combustion of Solids in Microgravity, Results from the BASS-II Experiment | https://ntrs.nasa.gov/citations/20160000593 |
| NTRS 20140011099: Thickness and Fuel Preheating Effects on Material Flammability in Microgravity from the BASS Experiment | https://ntrs.nasa.gov/archive/nasa/casi.ntrs.nasa.gov/20140011099.pdf |
| NASA Science in Space: Fire Safety in Space (Sept. 29, 2023) | https://www.nasa.gov/missions/station/iss-research/science-in-space-week-of-sept-29-2023-fire-safety-in-space/ |
| NASA: ACME Project, The Space Station's Quest for the Secrets of Fire | https://www.nasa.gov/humans-in-space/space-stations-quest-for-the-secrets-of-fire/ |
| NASA Glenn: ACME paper (CSSCI 2022 Spring Technical Meeting) | https://www1.grc.nasa.gov/wp-content/uploads/ACME-CSSCI-paper-20220402.pdf |
| NASA: Studying Combustion and Fire Safety (CIR, FLEX, FLEX-2, ACME) | https://www.nasa.gov/missions/station/iss-research/studying-combustion-and-fire-safety/ |
| NASA ISS On-Orbit Status Report, 10 April 2014 | https://www.nasa.gov/blogs/stationreport/2014/04/10/iss-daily-summary-report-04-10-14/ |
| NASA Space Apps 2026 challenge page | https://www.spaceappschallenge.org/2026/challenges/flame-in-freefall-ai-powered-fire-safety-insights-from-microgravity-combustion-data/ |

## Disclaimer

This is an unofficial prototype created for the NASA Space Apps Challenge. It is not affiliated with, endorsed by, or an official product of NASA. Flame AI is a rule-based project feature and is not an official NASA AI system. Content is for education and research exploration only and must not be used for real-world fire-safety or engineering decisions. Always consult the original NASA sources linked above.
