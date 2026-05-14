# Scheduler Module

The `scheduler` module is a **reusable Django app** that implements the astronomical observation scheduling engine. It can be integrated into any Django/TOM Toolkit project by adding it to `INSTALLED_APPS`.

## Installation as a dependency

The module is available as a local package:

```toml
# In the main project's pyproject.toml
scheduler = {path = "../scheduler", develop = true}
```

Or once published on PyPI:

```bash
pip install ria-scheduler
```

## Configuration in `settings.py`

```python
INSTALLED_APPS = [
    ...
    "scheduler",
    ...
]

# Default observatory location
OBSERVER_LATITUDE = 28.3011
OBSERVER_LONGITUDE = -16.5106
OBSERVER_ELEVATION = 2390  # metres
OBSERVER_NAME = "Teide Observatory"
```

## Integration with TOM Toolkit

The app integrates automatically into the TOM interface:

- **Navbar**: Adds the "Scheduler" link to the main menu via `navbar_items()`.
- **Target Detail**: Adds the "Scheduler Config" button to the target detail view via `target_detail_buttons()`.

## Internal architecture

| File / Directory | Description |
|---|---|
| `models.py` | `SchedulingProfile`, `FacilitySettings`, `ScheduledWindow`, etc. |
| `views.py` | `TimelineView` + JSON API endpoints |
| `forms.py` | `SchedulingProfileForm`, `ObservationFramesFormSet`, etc. |
| `urls.py` | Frontend and API routes |
| `astronomy.py` | `AstronomyEngine` |
| `weather.py` | `WeatherService` |
| `ascom_controller.py` | ASCOM telescope control |
| `apps.py` | `SchedulerConfig` (TOM integration) |
| `scoring_strategies/` | Per-category scoring strategies |
| `allocation_strategies/` | Time-slot allocation algorithms |
| `management/commands/` | `run_scheduler` CLI management command |
| `templates/` | Scheduler HTML templates |
| `static/` | CSS and JS for the frontend |
| `tests/` | Test suite |
