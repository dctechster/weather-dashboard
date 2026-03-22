# Weather Dashboard

A single-file, no-build weather dashboard built with vanilla HTML, CSS, and JavaScript. No API key required.

<img width="600" height="auto" alt="WeatherDashboard-Main" src="https://github.com/user-attachments/assets/34b1e721-a631-4f99-81fb-ef92b3a7b62a" />
## Features

### Current Conditions

- Temperature (°F / °C toggle), description, and feels-like
- Dynamic background that changes with weather conditions and time of day
- Configurable info row: Sunrise, Sunset, Cloud cover, Wind speed, Wind direction (with rotated compass arrow), Gusts, Humidity, Pressure, Visibility, UV Index, Precipitation, Air Quality

### Forecasts
- **Hourly strip** — next 24 hours with icon, temperature, and precipitation probability
- **7- or 14-day forecast** — daily high/low bar, weather icon, and precipitation probability
- **Chart** — 24-hour line chart switchable between Temperature, Wind Speed, Humidity, and Precipitation Probability; toggle between Next 24h and Past 24h

### Detail Cards
- Humidity, Wind, Pressure, Visibility, UV Index, Precipitation, Air Quality (US AQI with color coding)

### City Management
- Search with live autocomplete (city name, state/territory, country)
- Detect current location via browser geolocation
- Save and switch between multiple cities
- Weather data cached for 10 minutes — unit and config changes re-render from cache with no extra network call

### Configuration
Click **Configure** to toggle any section or data point on or off. Preferences are saved to `localStorage` and persist across sessions. Turning off Detail Cards and enabling the same metrics in the Main Card produces a lean, single-card layout.

## Getting Started

No installation or build step needed.

1. Clone or download the repository
2. Open `weather_dashboard.html` in any modern browser
3. Search for a city or click **📍 My Location**

That's it. The app calls [Open-Meteo](https://open-meteo.com) directly from the browser — no server, no API key.

## Data Sources

| Data | API |
|------|-----|
| Weather & forecast | [Open-Meteo Forecast API](https://open-meteo.com) |
| Air quality | [Open-Meteo Air Quality API](https://open-meteo.com/en/docs/air-quality-api) |
| City search | [Open-Meteo Geocoding API](https://open-meteo.com/en/docs/geocoding-api) |
| Reverse geocoding (My Location) | [BigDataCloud Reverse Geocode](https://www.bigdatacloud.com/free-api/free-reverse-geocode-to-city-api) |

All APIs are free and require no authentication.

## Project Files

| File | Purpose |
|------|---------|
| `weather_dashboard.html` | The entire application |
| `weather-icon.svg` | Browser tab favicon and touch icon |
| `CHANGELOG.md` | Version history |
| `BACKLOG.md` | Known issues and planned improvements |

## Browser Support

Any modern browser that supports ES2020+ (Chrome, Firefox, Edge, Safari). SVG favicon support requires a browser released after 2020.

## License

MIT
