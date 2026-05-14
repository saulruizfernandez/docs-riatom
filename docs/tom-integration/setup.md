# Custom TOM Setup

## Django project structure

| Path | Description |
|---|---|
| `iac_tom/settings.py` | Global configuration |
| `iac_tom/urls.py` | Main URL router |
| `iac_tom/views.py` | `HomePageView`, `CreateTargetFromAlertView` |
| `iac_tom/facilities/` | Telescope facility adapters |
| `iac_tom/cadences/` | Cadence strategies |
| `iac_tom/wsgi.py` / `asgi.py` | WSGI/ASGI entry points |
| `manage.py` | Django management script |
| `db.sqlite3` | SQLite database (development) |
| `pyproject.toml` | Project dependencies and tooling |

## Installed apps

```python
INSTALLED_APPS = [
    # Django core
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
    # TOM Toolkit apps
    "tom_setup",
    "tom_targets",
    "tom_alerts",
    "tom_catalogs",
    "tom_common",
    "tom_dataproducts",
    "tom_observations",
    "tom_harvesting",
    # Third-party
    "django_tables2",
    "crispy_forms",
    "django_filters",
    # Project
    "scheduler",
]
```

## Authentication

The installation uses TOM Toolkit's `READ_ONLY` mode:

```python
AUTH_STRATEGY = "READ_ONLY"
```

This allows unauthenticated users to view targets and observations, but requires login to modify data. Change to `"LOCKED"` to require login for all views.

## Observatory configuration

The default observatory is configured in `settings.py`:

```python
OBSERVER_LATITUDE = 28.3011    # Teide Observatory
OBSERVER_LONGITUDE = -16.5106
OBSERVER_ELEVATION = 2390      # metres
OBSERVER_NAME = "Teide Observatory"
```

These values are used by `AstronomyEngine` when no different location is specified.

## URL routing

```python
# iac_tom/urls.py
urlpatterns = [
    path("", HomePageView.as_view(), name="home"),
    path("alerts/alert/create/", CreateTargetFromAlertView.as_view()),
    path("scheduler/", include("scheduler.urls")),
    # TOM Toolkit URLs included automatically
]
```

## Extra field: Redshift

```python
EXTRA_FIELDS = [
    {"name": "redshift", "type": "number", "default": 0},
]
```

Appears in the creation form and the detail view of sidereal targets.
