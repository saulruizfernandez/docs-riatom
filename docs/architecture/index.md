# System Overview

RIA TOM is a Django application composed of two major functional blocks: the **TOM** (target and observation management) and the **Scheduler** (planning engine). Both share the same database and communicate through related models.

## Modules

### ria-tom

Contains the Django project configuration and TOM customizations:

| Component | File | Description |
|-----------|------|-------------|
| Settings | `iac_tom/settings.py` | Global config (apps, facilities, observatory, DB) |
| URLs | `iac_tom/urls.py` | Main router |
| HomePageView | `iac_tom/views.py` | Dashboard with astronomical and weather data |
| CreateTargetFromAlertView | `iac_tom/views.py` | Target creation from alerts with inferred category |
| MyObservationFacility | `facilities/myfacility.py` | Example facility for telescopes without OCS |
| CustomOCSFacility | `facilities/custom_ocs.py` | Integration with standard OCS |
| MyCadenceStrategy | `cadences/mycadence.py` | Customizable cadence strategy placeholder |

### scheduler

Reusable Django app implementing the scheduling engine:

| Component | Description |
|-----------|-------------|
| Models | `SchedulingProfile`, `FacilitySettings`, `ScheduledWindow`, `ObservationBlock`, `ObservationFrames` |
| AstronomyEngine | Visibility, altitude, airmass, lunar phase calculations |
| WeatherService | Forecasts from the 7Timer API |
| ScoringStrategies | Per-category scoring (Standard, Periodic, Transient, NonSidereal) |
| AllocationStrategy | Greedy time-slot allocation algorithm |
| ASCOM Controller | Optional robotic telescope control (Windows) |
| REST API | JSON endpoints for the interactive frontend |
