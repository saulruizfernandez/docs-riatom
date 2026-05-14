# Weather Module

The weather module fetches and processes 7Timer forecasts and integrates them into the scheduling pipeline.

## WeatherService

```python
from scheduler.weather import WeatherService

service = WeatherService(lat=28.3001, lon=-16.5097)
forecast = service.parse_forecast()
current = service.get_current_data()
```

## 7Timer API

The `ASTRO` endpoint of 7Timer is used, specifically designed for astronomy:

```
http://www.7timer.info/bin/api.pl
  ?lon={lon}&lat={lat}&ac=0&unit=metric&output=json&product=astro
```

The response includes data every 3 hours for the next 7 days. No authentication required.

## Remapping tables

The `weather_remapping.json` file contains the conversion tables between 7Timer's internal scales and interpretable values:

### Cloud cover (`cloudcover`)

Scale 1–9, where 1 = clear and 9 = fully overcast.

### Seeing (`seeing`)

Scale 1–8, where 1 = excellent seeing (< 0.5") and 8 = very bad seeing (> 3.0").

### Transparency (`transparency`)

Scale 1–8, where 1 = photometric and 8 = very turbid.

### Wind (`wind10m`)

Scale 1–13 (adapted Beaufort knots), converted to m/s.

## Methods

### `fetch_api_data()`

Performs the HTTP request to 7Timer and returns the raw JSON. Raises an exception if the service is unavailable.

### `get_current_data()`

Returns current conditions as a dictionary:

```python
{
    "cloudcover": "13%",
    "seeing": "0.75-1.0\"",
    "transparency": "clear",
    "wind_speed": "5.7 m/s",
    "timestamp": "2024-01-15T21:00:00Z"
}
```

### `parse_forecast()`

Returns the full forecast list (up to 7 days), each entry with the same format as `get_current_data()` plus a `timepoint` field (hours from now).

## Integration with the scheduler

`SchedulerCore` receives the forecast as an optional parameter:

```python
weather = WeatherService()
forecast = weather.parse_forecast()

core = SchedulerCore()
windows = core.generate_candidate_windows(
    start_time=night_start,
    end_time=night_end,
    weather_forecast=forecast
)
```

The core finds the forecast entry closest to each candidate window and discards those that do not meet the profile's seeing and transparency constraints.
