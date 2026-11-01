# Lanka Explorer — Discover the Pearl of the Indian Ocean

**Mini Project — Individual submission (Weeks 01–05)**

| | |
|---|---|
| **Student name** | W.T.D. Rajarathna |
| **Student ID** | OTH/IT/IT_IFSL_001/B004/0023 |
| **Project type** | Individual mini project — all four lab roles covered by one person |
| **Live site (GitHub Pages)** | https://mrthamitha.github.io/lanka-explorer/ |
| **Repository** | https://github.com/mrthamitha/lanka-explorer |
| **Submission** | `W.T.D.Rajarathna_MiniProject.zip` → `Week 01/` … `Week 05/` + `Hosted Link.txt` |


---

## 1. What Lanka Explorer is

Lanka Explorer is a travel guide for Sri Lanka: ten researched destinations across four
categories (Heritage, Beach, Nature, Wildlife), with live weather, entry fees in US dollars
**and** rupees, an interactive map, and a personal trip planner that remembers your choices.

**The problem it solves.** Planning a trip around Sri Lanka normally means jumping between a
guide site for the places, a bank site for the exchange rate, a weather app for the season
and a spreadsheet for the budget. Lanka Explorer puts all four in one place, on one page
that works on a phone.

---

## 2. Feature list

| Area | Feature |
|---|---|
| **Data** | 10 destinations with 11 fields each in `data/destinations.json` — no card is hard-coded in HTML |
| **Home** | Hero banner, four category cards, three featured destinations generated from the JSON, "Why Lanka Explorer?" grid |
| **Destinations** | Search (name, district and description), category filter, sort by name / fee (low→high, high→low), live result counter, "Clear filters" button, map with a pin for every destination |
| **Details** | One page for all ten places (`details.html?id=…`), quick facts, live current weather + 3-day forecast, fee converted to rupees, single-pin map, add/remove trip button, "Destination not found" for a bad id |
| **My Trip** | Chosen destinations in a table, fee per person in USD and LKR, traveller count, total for the whole group, Remove buttons, Clear trip, empty-trip message — all saved in `localStorage` |
| **About** | Introduction, and an enquiry form with JavaScript validation (name, email, future date, 1–20 travellers, at least one interest, 500-character message) |
| **Theme** | Light mode and dark mode, remembered between visits; follows the operating system on a first visit |
| **Responsive** | Designed for 375px → 1280px+, hamburger menu below 768px, no horizontal scrolling |
| **Reliability** | Spinners while loading; friendly messages when the weather, currency or map service is unavailable |
| **Accessibility** | Semantic landmarks, one `<h1>` per page, visible focus outlines, labels on every field, alt text on every photo, WCAG AA colour contrast, `prefers-reduced-motion` support |

---

## 3. APIs used

| Service | Endpoint | Why it needs no key |
|---|---|---|
| **Open-Meteo** | `https://api.open-meteo.com/v1/forecast?latitude=…&longitude=…&current=…&daily=…&timezone=Asia%2FColombo&forecast_days=3` | free for non-commercial use |
| **Frankfurter** | `https://api.frankfurter.dev/v2/rate/usd/lkr` | public reference rates from central banks |
| **Leaflet 1.9.4 + OpenStreetMap** | `https://tile.openstreetmap.org/{z}/{x}/{y}.png` | free map tiles with attribution |

Details, sample responses and what I learned from each documentation: **`API-NOTES.md`**.

---

## 4. How to run the site

1. Open the `lanka-explorer` folder in VS Code (**File → Open Folder**).
2. Right-click `index.html` → **Open with Live Server**.
3. Check the address bar shows `http://127.0.0.1:5500/`.

The site **must** be served over HTTP — opening `index.html` from disk (`file:///…`) blocks
`fetch()`, so no cards, weather or maps would load. To see the data file itself, open
`http://127.0.0.1:5500/data/destinations.json`.

---

## 5. Project structure

```
lanka-explorer/
├── index.html              Home
├── destinations.html       All destinations (search, filter, sort, map)
├── details.html            One destination (?id=1 … ?id=10)
├── trip.html               My Trip planner
├── about.html              About + enquiry form
├── README.md               This file
├── API-NOTES.md            Week 04 API documentation notes
├── css/style.css           Theme, Flexbox, Grid, responsive, dark mode (sections 1–15)
├── js/app.js               Menu, data loading, cards, search, APIs, trip, dark mode
├── data/destinations.json  10 destinations × 11 fields
├── images/                 10 photos, all 1200 × 800 px
└── screenshots/            Every page at 375px and 1280px, light and dark (20 files)
```

`js/app.js` is organised under numbered headings (1, 1b, 2, 3, 4, 5, 5b–5e, 6, 6b, 7, 8) and
`css/style.css` under 1–15, so any part can be found in seconds during the demo.

---

## 6. Testing

### Full checklist (passed at both 1280px and 375px)

| Area | Test | Result |
|---|---|---|
| Navigation | Every link works on all five pages; mobile menu opens and closes | ✅ |
| Home | Featured cards load from JSON (3 places, correct fee bands) | ✅ |
| Destinations | Search + category + sort work together; count correct ("Showing 2 of 10") | ✅ |
| Destinations | Map shows every pin; popups link to the right details page | ✅ |
| Details | Correct place for every id; "not found" for id=99 | ✅ |
| Details | Weather, 3-day forecast, LKR fee and map all appear | ✅ |
| Details | Add/remove button works and survives a reload | ✅ |
| My Trip | Rows, totals and traveller count correct (verified by hand for 1 and 4 travellers) | ✅ |
| My Trip | Remove and Clear trip work; empty-trip message appears | ✅ |
| About | Form shows every validation error together, then a success message | ✅ |
| Dark mode | Every page readable in both themes; choice survives a reload | ✅ |
| Errors | Blocking each API shows a friendly message and nothing crashes | ✅ |
| Console | No red errors on any page | ✅ |
| Validation | HTML passes the W3C Nu validator; CSS passes a strict CSS3 parser; JSON passes JSONLint | ✅ |

### Lighthouse (Chrome DevTools → Lighthouse, Mobile)

| Page | Performance | Accessibility | Best practices | SEO |
|---|---:|---:|---:|---:|
| `index.html` | 83 | **100** | 100 | 100 |
| `destinations.html` | 73 | **96** | 100 | 100 |
| `details.html?id=1` | 63 | **100** | 100 | 100 |

Accessibility is well above the 90 target. Two issues Lighthouse reported were fixed:

1. **Colour contrast** — the Beach (`#1e88e5` → `#1565c0`) and Wildlife (`#e65100` → `#b23a00`)
   badges, and the result counter, did not reach a 4.5:1 ratio with white text.
2. **Colour contrast on the destinations page** — `#result-count` used the grey text colour;
   it now uses `--primary-dark`.

The one remaining warning is `target-size` on the Leaflet map pins. That markup is generated
by the Leaflet library itself, not by my HTML, and it does not affect the score below 96.
Performance is lower on the pages with live data because the photos, the weather request and
the map tiles all load together — the honest number for a free static host with no CDN.

---

## 7. How to publish on GitHub Pages

1. Create a **public** repository called `lanka-explorer` on GitHub.
2. Upload the **contents** of the `Week 05/lanka-explorer` folder (not the folder itself), so
   `index.html` sits at the top level of the repository. *Add file → Upload files* keeps the
   `css/`, `js/`, `data/` and `images/` folders if you drag the whole folder in.
3. **Settings → Pages → Build and deployment** → Source: *Deploy from a branch* → Branch:
   `main`, folder `/ (root)` → **Save**.
4. After a minute or two the site appears at
   `https://mrthamitha.github.io/lanka-explorer/`.
5. Open that link on a phone using mobile data and check: every image loads (GitHub Pages is
   case-sensitive — `Ella.jpg` and `ella.jpg` are different files online), all three APIs
   answer, and both maps draw.
6. Paste the link into `Hosted Link.txt` and into the table at the top of this README.

---

## 8. What I would improve with more time

- Serve the photos as WebP with `srcset` — the JPEGs are the main reason Performance is in
  the 60s–80s on a throttled connection.
- Cache the weather for a few minutes in `sessionStorage`, the way the exchange rate is
  cached, so moving between details pages does not repeat the request.
- Add a "nearest destination to me" feature using the browser's geolocation API and the
  `lat`/`lng` values that are already in the JSON.
- Replace the fixed `Asia/Colombo` timezone with a per-destination value if the app ever
  covers more than one country.

---

## 9. Reflection

**What I built.** Over five weeks I built a complete front-end web application on my own, starting
from an empty folder: five semantic HTML pages, a validated JSON data file with ten researched
destinations, a themed and fully responsive stylesheet with light and dark modes, and roughly
760 lines of organised JavaScript that loads the data, generates every card, searches, filters,
sorts, calls three external APIs, and keeps a personal trip planner in `localStorage`. Because
this was an individual project I covered the Project Lead, Content, Data and UI roles myself,
which meant every bug was also mine to find.

**The hardest problem.** The hardest part was not any single API — it was keeping several
independent sources of truth in step. The Destinations page has to redraw cards, keep the
"Showing X of 10" counter correct, **and** hide the pins on the map when a filter changes, all
from one `updateList()` function. My first version drew the map inside `updateList()` and
created a new Leaflet map on every keystroke, which eventually froze the page. I fixed it by
drawing the map once, keeping the marker objects in an array, and writing a small
`filterMapMarkers()` helper that only adds or removes layers. The second-hardest problem was the
"Free (Free)" bug in Week 05 — understanding that `formatFee()` and `getPriceLabel()` both
return the word "Free" taught me to check what each helper is responsible for instead of
patching the display string.

**What I would improve.** I would name my CSS variables by role (`--surface`, `--text`,
`--text-on-image`) instead of by colour, because `--white` becoming a dark grey in dark mode
forced me to write extra "fixed colour" rules — a lesson the Week 05 guide predicted. I would
also add loading skeletons instead of a single spinner, and cache API responses so a weak
mobile connection is not punished twice for the same data.
