# Components

## TOM Core (TOM Toolkit)

RIA TOM extends [TOM Toolkit](https://tom-toolkit.readthedocs.io/) without modifying its source code, using only the official extension mechanisms:

- **`tom_targets`** — Sidereal and non-sidereal target management. An extra `redshift` field is added for sidereal targets.
- **`tom_observations`** — Observation records and status tracking.
- **`tom_dataproducts`** — Data product storage (photometry, FITS files, spectra, images).
- **`tom_alerts`** — Subscriptions to astronomical alert brokers (ALeRCE, Gaia, Lasair, TNS).
- **`tom_catalogs`** — External catalogue queries.

## AstronomyEngine

The central class that encapsulates all astronomical calculations using `astroplan` and `astropy`.

**Inputs**: observatory coordinates (lat, lon, elevation), target (RA/Dec or ephemerides), reference time.

**Outputs**:

| Method | Description |
|--------|-------------|
| `get_skycoord()` | Equatorial coordinates of the target |
| `get_target_altaz_coords()` | Altitude and azimuth at a given instant |
| `get_airmass()` | Airmass (atmospheric path length) |
| `get_moon_separation()` | Angular distance to the Moon in degrees |
| `get_moon_phase()` | Lunar illumination fraction [0.0, 1.0] |
| `get_observing_window()` | Astronomical night window |
| `get_visibility_curve()` | Altitude curve over a time grid |

## WeatherService

Fetches and processes forecasts from [7Timer](http://www.7timer.info/), a free astronomical weather service. Translates the API's internal scales to interpretable values:

- **Cloud cover**: scale 1–9 → percentage coverage (3%–97%)
- **Seeing**: scale 1–8 → arcsecond ranges (0.35"–3.0")
- **Transparency**: scale 1–8 → quality labels and photometric category
- **Wind**: scale 1–13 → speed in m/s

## Scoring Strategies

Each target category has its own scoring strategy:

| Strategy | Category | Core logic |
|----------|----------|-----------|
| `StandardStrategy` | STANDARD | Weighted average of altitude, airmass, lunar distance, priority |
| `PeriodicStrategy` | PERIODIC | Gaussian centred on mid-transit; 0 outside the window |
| `TransientStrategy` | TRANSIENT | Exponential decay: urgency halves every 6 hours |
| `NonSiderealStrategy` | NON_SIDEREAL | Ephemeris-based positions for asteroids and comets |

## AllocationStrategy (Greedy)

The greedy algorithm sorts candidates by score (descending) and assigns free time slots. If two targets overlap, the one with the higher score is kept. The `ideal_center` parameter allows adjusting the preferred position within each free interval.

## ASCOM Controller

Optional module for direct control of robotic telescopes compatible with the [ASCOM](https://ascom-standards.org/) standard. Executes the observation sequence in a background thread, slewing to the target at the scheduled time. **Microsoft Windows only.**

## Facilities

Facilities are adapters between TOM Toolkit and the various telescope control systems:

| Facility | Description |
|----------|-------------|
| `MyObservationFacility` | Generic example for telescopes without OCS |
| `CustomOCSFacility` | Integration with standard OCS (LCO-compatible) |
| LCO (built-in) | Las Cumbres Observatory (included in TOM Toolkit) |
| GEM (built-in) | Gemini Observatory GS and GN (included in TOM Toolkit) |
