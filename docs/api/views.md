# Views API Reference

## Django views

### HomePageView

**URL**: `/`
**Class**: `iac_tom.views.HomePageView`
**Inheritance**: `TemplateView`

Main dashboard. Shows the astronomical status of the night and the weather forecast.

**Context**:

| Variable | Description |
|----------|-------------|
| `targets` | Most recently added targets |
| `astronomy_engine` | `AstronomyEngine` instance for the current observatory |
| `weather_service` | `WeatherService` instance |
| `user_location` | Observatory coordinates (from session or settings) |
| `night_type` | `dark`, `gray`, or `bright` based on lunar illumination |

**POST**: Updates the observatory coordinates in the user's session.

---

### CreateTargetFromAlertView

**URL**: `/alerts/alert/create/`
**Class**: `iac_tom.views.CreateTargetFromAlertView`
**Inheritance**: `tom_alerts.views.CreateTargetFromAlertView`

Extends the standard TOM view to:

1. Infer the scheduling category from broker metadata (especially ALeRCE `lc_classifier_top`).
2. Automatically create an empty `SchedulingProfile`.
3. Assign the target to the user's group.

---

### TimelineView

**URL**: `/scheduler/timeline/`
**Class**: `scheduler.views.TimelineView`
**Inheritance**: `LoginRequiredMixin`, `TemplateView`

Main scheduler view. Supports AJAX requests for partial table updates.

**Context**:

| Variable | Description |
|----------|-------------|
| `all_profiles_qs` | All profiles with an assigned facility |
| `has_missing_telescope` | `True` if any active profile lacks a facility |
| `observation_blocks` | Active blocks with their members |
| `block_profile_ids` | Set of profile IDs belonging to a block |
| `profiles` | Paginated profiles (4 per page) |
| `scheduled_windows` | Future windows ordered by `start_time` |
| `scheduled_groups` | TargetLists containing scheduled targets |
| `addable_groups` | TargetLists without scheduled targets |
| `all_groups` | All TargetLists |

---

## API Endpoints (JSON)

### Targets

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/scheduler/api/search-targets/` | GET | Search targets by name. Parameter: `q` |
| `/scheduler/api/nightly/` | GET | Targets scheduled for tonight |
| `/scheduler/api/targets/<id>/analyze-photometry/` | GET | Light curve analysis for a target |

### Scheduled windows

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/scheduler/api/events/` | GET | Windows for the calendar (FullCalendar) |
| `/scheduler/api/update/<window_id>/` | POST | Update start/end of a window |
| `/scheduler/api/gantt/` | GET | Data for the Gantt chart |
| `/scheduler/api/visibility/` | GET | Visibility curve for a target |
| `/scheduler/api/export-schedule/` | GET | Export schedule to CSV |

### Astronomical night

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/scheduler/api/night-times/` | GET | Night start and end times |
| `/scheduler/api/night-window/create/` | POST | Create a manual `NightObservationWindow` |

### Profiles

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/scheduler/api/profile/<id>/toggle-active/` | POST | Enable/disable a profile |

### Observation blocks

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/scheduler/api/block/create/` | POST | Create a new block |
| `/scheduler/api/block/<id>/delete/` | DELETE | Delete a block |
| `/scheduler/api/block/<id>/add-target/` | POST | Add a target to the block |
| `/scheduler/api/block/<id>/remove-target/` | POST | Remove a target from the block |
| `/scheduler/api/upload-blocks/` | POST | Import blocks from CSV |
| `/scheduler/api/preview-blocks/` | POST | Preview CSV before importing |

### Weather

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/scheduler/api/weather-forecast/` | GET | Full processed weather forecast |

### ASCOM

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/scheduler/api/ascom/start/` | POST | Start the ASCOM control sequence |
| `/scheduler/api/ascom/stop/` | POST | Stop the ASCOM sequence |
| `/scheduler/api/ascom/status/` | GET | Current telescope state |
