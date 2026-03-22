# Weather Dashboard — Changelog

## 2026-03-21 (2)

### Added

#### Configuration mode
A "Configure" button in the header opens a panel where every detail card and major
section can be individually toggled on or off. Preferences are persisted to
`localStorage` under the key `wdConfig` and applied on every render.

Toggleable items:
- **Detail Cards**: Humidity, Wind, Pressure, Visibility, UV Index, Precipitation,
  Air Quality
- **Sections**: Hourly Forecast, 7-Day Forecast, Temperature Chart

Implementation notes:
- `CONFIG_DEFAULTS` defines the default on/off state for all 10 keys.
- `config` is merged from defaults + stored JSON on startup so new keys pick up
  their defaults even for returning users.
- `renderConfigPanel()` rebuilds toggle buttons from the current config state;
  `setConfig(key)` flips one key, persists, re-renders the panel, then re-renders
  the weather content from cache (no network call).
- `toggleConfig()` shows/hides the panel and marks the button active.

#### Past weather chart
The temperature chart now has a "Next 24h / Past 24h" toggle rendered directly
in the chart section header. The `chartMode` state variable (`'forecast'` or
`'past'`) is passed into `buildChart()`, which slices the hourly array accordingly:
- **Forecast**: 24 hours starting from the current hour (existing behaviour)
- **Past**: 24 hours ending at the current hour

The weather API request now includes `past_days=1` so past hourly data is always
available. The in-memory cache key is unchanged (`lat,lon`) so unit switches and
config changes still serve from cache. `setChartMode(mode)` re-renders from cache
with no network call.

## 2026-03-21 (1)

### Fixed

#### #1 — XSS: sanitize city name/country in innerHTML
Added `esc()` helper that escapes `&`, `<`, `>`, `"`, and `'` to their HTML entity equivalents.
Applied to all places where city name or country code from the geocoding API are interpolated
into `innerHTML`:
- `render()` — hero location heading
- `loadCity()` — loading state message

#### #2 — Attribute injection in renderSavedPills onclick handler
Replaced the inline `onclick` attributes that embedded `JSON.stringify(city)` directly into
HTML attribute values with `data-idx` / `data-del-idx` integer attributes and proper
`addEventListener` calls added after the `innerHTML` assignment. City objects are now
resolved from the `savedCities` array by index at event time, eliminating the injection
surface entirely. `esc()` is also applied to city name/country in the pill text.

#### #3 — Metric units for wind speed, visibility, and precipitation
All three values are fetched in imperial units (the API call is unchanged, keeping the
in-memory cache valid across unit switches) and converted client-side when `unit === 'C'`:

| Field | Imperial | Metric | Conversion |
|-------|----------|--------|------------|
| Wind speed (detail card) | mph | km/h | × 1.60934 |
| Wind gusts (hero extras) | mph | km/h | × 1.60934 |
| Visibility | mi | km | meters ÷ 1000 |
| Precipitation | in | mm | × 25.4 |

A `visDisplay` variable is computed alongside the existing `visMi` (retained for the
Clear / Moderate / Poor threshold comparison which remains in miles regardless of unit).
