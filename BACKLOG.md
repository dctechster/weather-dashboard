# Weather Dashboard — Backlog

| # | Subject | Priority | Status |
|---|---------|----------|--------|
| 1 | Fix XSS risk: sanitize city name/country in innerHTML | High | Done |
| 2 | Fix attribute injection in renderSavedPills onclick handler | High | Done |
| 3 | Respect metric units for wind speed, visibility, and precipitation | Medium | Done |
| 4 | Add resize listener to re-render SVG chart on window resize | Medium | Open |
| 5 | Fix float lat/lon deduplication in saveCity | Medium | Open |
| 6 | Add loading feedback to doSearch() | Low | Open |
| 7 | Refactor acDiv._results to a closure variable | Low | Open |
| 8 | Add configuration mode to toggle detail cards and sections | Medium | Done |
| 9 | Add Past 24h / Next 24h toggle to temperature chart | Medium | Done |

---

## Details

### #1 — Fix XSS risk: sanitize city name/country in innerHTML (High)
`city.name` and `city.country` from the geocoding API are interpolated directly into `innerHTML`
in `render()` (line 567) and `renderSavedPills()` (line 675). A maliciously crafted city name
could inject arbitrary HTML. Escape these values before inserting into `innerHTML`.

### #2 — Fix attribute injection in renderSavedPills onclick handler (High)
`renderSavedPills()` uses `JSON.stringify(c).replace(/"/g,'&quot;')` to embed city data as an
inline `onclick` attribute value (line 677). This is incomplete sanitization — single quotes or
backticks in a city name could break out of the attribute. Use event listeners with a data map
instead of inline `onclick` with serialized JSON.

### #3 — Respect metric units for wind speed, visibility, and precipitation (Medium)
`fetchWeather()` hardcodes `wind_speed_unit=mph` and `precipitation_unit=inch` in the API request
(line 383), and `render()` always displays "mph" (line 594) and converts visibility from meters to
miles (line 491). When the user selects °C, these should switch to km/h and mm respectively.
The API supports `wind_speed_unit=kmh` and `precipitation_unit=mm` — pass the correct unit based
on the current `unit` setting, or fetch both and convert client-side like temperatures.

### #4 — Add resize listener to re-render SVG chart on window resize (Medium)
The code comments "Re-render chart on resize for responsiveness" (line 651) but no resize event
listener is attached. The SVG scales visually via CSS `width:100%` but label positions and dot
placements are calculated at render time and don't adapt. Add a debounced `window` resize listener
that rebuilds the chart when an active city is loaded.

### #5 — Fix float lat/lon deduplication in saveCity (Medium)
City deduplication in `saveCity()` uses exact float equality (`c.lat === city.lat && c.lon === city.lon`)
at line 660. The same city can appear with slightly differing coordinates from two separate geocode
calls, creating duplicates. Switch to a tolerance-based comparison (e.g. `Math.abs` difference < 0.01)
or deduplicate by name + country.

### #6 — Add loading feedback to doSearch() (Low)
`doSearch()` (line 738) awaits `geocode()` silently with no UI feedback. On slow networks the search
button appears unresponsive. Disable the button or show a spinner on the input while the geocode
request is in flight, then restore it on completion or error.

### #7 — Refactor acDiv._results to a closure variable (Low)
`runAutocomplete()` stores geocode results as a property on a DOM element (`acDiv._results`, line 713).
Move results into a module-level variable (e.g. `let acResults = []`) that is set alongside the DOM
update and read in the click handler.

### #8 — Add configuration mode to toggle detail cards and sections (Medium)
A "Configure" button in the header opens a panel where every detail card and major section can be
individually toggled on or off. Preferences are persisted to `localStorage` under the key `wdConfig`
and applied on every render.

Toggleable items: Humidity, Wind, Pressure, Visibility, UV Index, Precipitation, Air Quality,
Hourly Forecast, 7-Day Forecast, Temperature Chart.

`CONFIG_DEFAULTS` defines the default on/off state for all 10 keys. `config` is merged from
defaults + stored JSON on startup. `renderConfigPanel()` rebuilds toggle buttons from the current
config state; `setConfig(key)` flips one key, persists, re-renders the panel, then re-renders
weather content from cache (no network call). `toggleConfig()` shows/hides the panel.

### #9 — Add Past 24h / Next 24h toggle to temperature chart (Medium)
The temperature chart has a "Next 24h / Past 24h" toggle rendered in the chart section header.
The `chartMode` state variable (`'forecast'` or `'past'`) is passed into `buildChart()`, which
slices the hourly array accordingly:
- **Forecast**: 24 hours starting from the current hour
- **Past**: 24 hours ending at the current hour

The weather API request includes `past_days=1` so past hourly data is always available. The
in-memory cache key is unchanged (`lat,lon`). `setChartMode(mode)` re-renders from cache with
no network call.
