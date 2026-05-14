# TOM Integration Overview

RIA TOM extends [TOM Toolkit](https://tom-toolkit.readthedocs.io/) using only its official extension mechanisms, without modifying the framework's source code.

## Implemented extensions

| Extension | Class | Description |
|-----------|-------|-------------|
| Home view | `HomePageView` | Dashboard with astronomical and weather data |
| Alert creation | `CreateTargetFromAlertView` | Automatically infers target category |
| Custom facility | `MyObservationFacility` | Adapter for telescopes without OCS |
| OCS facility | `CustomOCSFacility` | Integration with standard OCS |
| Cadence | `MyCadenceStrategy` | Customisable cadence strategy placeholder |
| Extra field | `redshift` | Additional field on sidereal targets |
| Scheduler app | `scheduler` | Full scheduling engine |

## Configured alert brokers

| Broker | Alert source |
|--------|-------------|
| ALeRCE | ZTF, automatic ML classification |
| Gaia | Gaia Science Alerts |
| Lasair | Processed ZTF alerts (UK) |
| TNS | Transient Name Server (IAU) |

## Configured facilities

| Facility | Description |
|----------|-------------|
| LCO | Las Cumbres Observatory (built into TOM Toolkit) |
| GEM-GS | Gemini South (built into TOM Toolkit) |
| GEM-GN | Gemini North (built into TOM Toolkit) |
| CustomOCS | Custom OCS facility |
| MyObservationFacility | Example facility |

## Configured cadence strategies

| Strategy | Description |
|----------|-------------|
| `RetryFailedObservations` | Retries failed observations |
| `ResumeCadenceAfterFailure` | Resumes cadence after a failure |
| `MyCadenceStrategy` | Customisable strategy (placeholder) |
