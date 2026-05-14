# RIA TOM Toolkit

**RIA TOM** (Red de Infraestructuras de Astronomía — Target and Observation Manager) is an astronomical observation planning tool developed at the **Instituto de Astrofísica de Canarias (IAC)** for the Spanish Astronomy Infrastructure Network (RIA).

## What is RIA TOM?

Astronomers typically use a variety of disconnected tools to plan their observations, even when using a TOM system to manage their targets. RIA TOM integrates all the necessary functionalities for the complete observation cycle into a single platform:

- **Target management**: Built on [TOM Toolkit](https://tom-toolkit.readthedocs.io/), supports sidereal, non-sidereal, periodic, and transient objects.
- **Visibility calculations**: Uses `astropy` and `astroplan` to compute altitude, airmass, lunar distance, and observability windows.
- **Automatic scheduling**: The Scheduler module prioritizes and sorts candidate targets, generating an optimized nightly plan.
- **Weather integration**: Queries real-time forecasts to reject windows with poor sky conditions.
- **Telescope control**: Optional support for sending observations to robotic telescopes via ASCOM or OCS-compatible facilities.

## General Architecture

The project consists of two main modules:

| Module | Description |
|--------|-------------|
| `ria-tom` | Django project that configures the TOM and main views |
| `scheduler` | Reusable Django app containing the scheduling engine |

## Quick Start

```bash
cd ria-tom
docker compose up --build
```

See the [Installation](getting-started/installation.md) guide for full details.

## Project Structure

```
root-dir/
├── ria-tom/            # Main Django project (TOM)
│   └── iac_tom/        # Settings, URLs, views, facilities, cadences
└── scheduler/          # Django app — scheduling engine
    └── scheduler/      # Models, views, strategies, utilities
```

## Technology Stack

- **Web framework**: Django 4.2 + TOM Toolkit 2.30
- **Astronomy**: astropy 6.1, astroplan 0.10
- **Data**: SQLite (development), PostgreSQL (recommended for production)
- **Frontend**: Bootstrap, FullCalendar (interactive timeline)
- **Code quality**: Ruff, djlint, pre-commit
