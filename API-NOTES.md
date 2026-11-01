# API-NOTES.md — Week 04

Three external services power this week's features. None of them needs an API key,
so there is nothing secret in the front-end code.

---

## 1. Open-Meteo — weather and 3-day forecast

| | |
|---|---|
| **Base URL** | `https://api.open-meteo.com/v1/forecast` |
| **Used on** | `details.html` (`showWeather()`) |
| **Key needed** | No — free for non-commercial use |
| **Docs** | https://open-meteo.com/en/docs |

**Endpoint used**

```
https://api.open-meteo.com/v1/forecast
  ?latitude={lat}
  &longitude={lng}
  &current=temperature_2m,relative_humidity_2m,wind_speed_10m,weather_code
  &daily=weather_code,temperature_2m_max,temperature_2m_min,precipitation_probability_max
  &timezone=Asia%2FColombo
  &forecast_days=3
```

**What I take from the response**

- `current.temperature_2m`, `current.relative_humidity_2m`, `current.wind_speed_10m`,
  `current.weather_code` → the "Weather Now" block.
- `daily.time[]`, `daily.weather_code[]`, `daily.temperature_2m_max[]`,
  `daily.temperature_2m_min[]`, `daily.precipitation_probability_max[]` → three forecast
  boxes. The arrays are **parallel**: index `i` belongs to the same day in every array, so
  one loop builds one box per day.

**One thing I learned from the documentation**

`timezone=Asia%2FColombo` is not optional for this app. Without it Open-Meteo returns times
in GMT, so a forecast for "today" south of the equator can start at 18:30 the evening
before. The `%2F` is the URL-encoded form of `/`. Also, `weather_code` is a **WMO code**, not
text, so I wrote `describeWeather()` (and `getWeatherIcon()` for an emoji) to translate it.

---

## 2. Frankfurter — USD → LKR exchange rate

| | |
|---|---|
| **Base URL** | `https://api.frankfurter.dev/v2/rate` |
| **Used on** | `details.html` (`showFeeInLkr()`), every card, and the My Trip totals |
| **Key needed** | No |
| **Docs** | https://frankfurter.dev |

**Endpoint used**

```
https://api.frankfurter.dev/v2/rate/usd/lkr
```

**Response**

```json
{ "date": "2026-11-05", "base": "USD", "quote": "LKR", "rate": 298.5 }
```

**What I take from the response**

`rate` — rupees for one US dollar. `entryFeeUSD * rate` is the price in rupees,
formatted with `Math.round(...).toLocaleString('en-LK')` so `10800.4` becomes `Rs. 10,800`.

**One thing I learned from the documentation**

Frankfurter publishes **reference rates from central banks**, updated once per working day —
not live trading rates. That is why the site says "≈" and shows the date-based rate. It also
means the rate does **not** change between two requests on the same day, so caching one
value in `usdToLkrRate` is both faster and friendlier to the free service (no rate-limit risk).

---

## 3. Leaflet + OpenStreetMap — interactive maps

| | |
|---|---|
| **Library** | `https://unpkg.com/leaflet@1.9.4/dist/leaflet.js` (+ `leaflet.css`) |
| **Tiles** | `https://tile.openstreetmap.org/{z}/{x}/{y}.png` |
| **Used on** | `details.html` (`showMap()`), `destinations.html` (`showAllDestinationsMap()`) |
| **Key needed** | No |
| **Docs** | https://leafletjs.com/reference.html |

**Calls used**

- `L.map('map').setView([lat, lng], 12)` — creates the map on a `<div>` and centres it.
- `L.tileLayer(...)` — loads the OpenStreetMap images; `{z}`, `{x}` and `{y}` are replaced
  for every tile automatically as the user pans and zooms.
- `L.marker([lat, lng]).addTo(map).bindPopup(html)` — the pin and its popup.
- `map.fitBounds(points, { padding: [30, 30] })` — on the Destinations page, picks the zoom
  that shows every pin at once.

**One thing I learned from the documentation**

The map `<div>` **must have an explicit height** (`#map { height: 350px; }`); Leaflet cannot
work out a height by itself, and an empty grey box is the classic symptom. The docs also
state that the OpenStreetMap attribution is a **requirement of their tile usage policy**, so
`addBaseLayer()` always adds it.

---

## Error handling used for all three

| Failure | How the code catches it |
|---|---|
| No network / request blocked | `fetch()` rejects, so the `catch` block runs |
| Server error (404, 500) | `response.ok` is `false`, so the code throws and `catch` runs |
| Leaflet script did not load | `typeof L === 'undefined'` check before any map code |
| Currency fails | The conversion is hidden quietly; the USD fee is still correct |
| Slow connection | `showLoading()` shows a spinner until the answer arrives |

I tested all three with DevTools → Network → *Block request URL*, and with 3G throttling,
on both the details page and the Destinations page.
