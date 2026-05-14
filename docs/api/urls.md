# URL Reference

## ria-tom (Main project)

| URL | View | Name |
|-----|------|------|
| `/` | `HomePageView` | `home` |
| `/alerts/alert/create/` | `CreateTargetFromAlertView` | — |
| `/scheduler/` | Includes `scheduler.urls` | — |

TOM Toolkit URLs (targets, observations, alerts, etc.) are included automatically.

## scheduler (App)

### Frontend views

| URL | View | Name |
|-----|------|------|
| `/scheduler/` | Redirect to timeline | — |
| `/scheduler/timeline/` | `TimelineView` | `scheduler:timeline` |
| `/scheduler/facilities/` | `FacilitySettingsListView` | `scheduler:facility-list` |
| `/scheduler/facilities/create/` | `FacilitySettingsCreateView` | `scheduler:facility-create` |
| `/scheduler/facilities/<pk>/update/` | `FacilitySettingsUpdateView` | `scheduler:facility-update` |
| `/scheduler/facilities/<pk>/delete/` | `FacilitySettingsDeleteView` | `scheduler:facility-delete` |
| `/scheduler/targets/<pk>/scheduler-config/` | `SchedulingProfileUpdateView` | `scheduler:profile-update` |
| `/scheduler/profile/<pk>/delete/` | `SchedulingProfileDeleteView` | `scheduler:profile-delete` |
| `/scheduler/target-groups/<pk>/constraints/` | `group_constraints_view` | `scheduler:group-constraints` |
| `/scheduler/run-scheduler/` | `trigger_scheduler_view` | `scheduler:run-scheduler` |

### API endpoints

| URL | Method | Name |
|-----|--------|------|
| `/scheduler/api/events/` | GET | `scheduler:api-events` |
| `/scheduler/api/update/<window_id>/` | POST | `scheduler:api-update-window` |
| `/scheduler/api/search-targets/` | GET | `scheduler:api-search-targets` |
| `/scheduler/api/profile/<id>/toggle-active/` | POST | `scheduler:api-toggle-profile` |
| `/scheduler/api/block/create/` | POST | `scheduler:api-block-create` |
| `/scheduler/api/block/<id>/delete/` | DELETE | `scheduler:api-block-delete` |
| `/scheduler/api/block/<id>/add-target/` | POST | `scheduler:api-block-add-target` |
| `/scheduler/api/block/<id>/remove-target/` | POST | `scheduler:api-block-remove-target` |
| `/scheduler/api/nightly/` | GET | `scheduler:api-nightly` |
| `/scheduler/api/night-times/` | GET | `scheduler:api-night-times` |
| `/scheduler/api/night-window/create/` | POST | `scheduler:api-night-window-create` |
| `/scheduler/api/gantt/` | GET | `scheduler:api-gantt` |
| `/scheduler/api/visibility/` | GET | `scheduler:api-visibility` |
| `/scheduler/api/weather-forecast/` | GET | `scheduler:api-weather` |
| `/scheduler/api/upload-blocks/` | POST | `scheduler:api-upload-blocks` |
| `/scheduler/api/preview-blocks/` | POST | `scheduler:api-preview-blocks` |
| `/scheduler/api/targets/<id>/analyze-photometry/` | GET | `scheduler:api-analyze-photometry` |
| `/scheduler/api/export-schedule/` | GET | `scheduler:api-export-schedule` |
| `/scheduler/api/ascom/start/` | POST | `scheduler:api-ascom-start` |
| `/scheduler/api/ascom/stop/` | POST | `scheduler:api-ascom-stop` |
| `/scheduler/api/ascom/status/` | GET | `scheduler:api-ascom-status` |
