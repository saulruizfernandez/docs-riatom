# Astronomy Utilities

The `AstronomyEngine` class encapsulates all astronomical calculations in the scheduler using the `astropy` and `astroplan` libraries.

## Initialisation

```python
from scheduler.astronomy import AstronomyEngine

engine = AstronomyEngine(
    lat=28.3011,
    lon=-16.5106,
    elevation=2390,
    name="Teide Observatory"
)
```

If no coordinates are provided, the values from `settings.py` are used:

```python
OBSERVER_LATITUDE = 28.3011
OBSERVER_LONGITUDE = -16.5106
OBSERVER_ELEVATION = 2390
OBSERVER_NAME = "Teide Observatory"
```

Internally creates an `astropy.EarthLocation` and an `astroplan.Observer` object.

## Main methods

### `get_skycoord(target, time)`

Returns the equatorial coordinates (`astropy.SkyCoord`) of the target at the given time. Supports:

- **Sidereal targets**: uses RA/Dec from the TOM Target model.
- **Non-sidereal targets**: computes position from ephemerides.

### `get_observing_window(start_time, end_time)`

Returns the astronomical night window (−18° twilight) within the specified period. If no range is given, returns the next night.

### `get_target_altaz_coords(target, time)`

Returns the horizontal coordinates (altitude, azimuth) of the target at instant `time`.

```python
altaz = engine.get_target_altaz_coords(target, Time.now())
print(altaz.alt.deg)  # altitude in degrees
```

### `get_airmass(target, time)`

Computes the airmass using the Rozenberg formula:

**X = 1 / (cos(z) + 0.025 × exp(−11 × cos(z)))**

Where `z` is the zenith angle. Returns `inf` if the target is below the horizon.

### `get_moon_separation(target, time)`

Returns the angular separation between the target and the Moon in degrees.

### `get_moon_phase(time)`

Returns the lunar disc illumination fraction [0.0, 1.0]:

- `0.0` = new Moon
- `0.5` = first/last quarter
- `1.0` = full Moon

### `is_target_visible(target, time)`

Boolean. Checks whether the target is above the horizon at the given instant.

### `get_visibility_curve(targets, start, end, sample_every)`

Generates an altitude curve for a list of targets over a time grid. The `sample_every` parameter controls the resolution in minutes (default: 10 min).

Returns a dictionary `{target_name: [(time, altitude), ...]}`.

## Diagnostic test

You can verify that `AstronomyEngine` is working correctly with the management command:

```bash
python manage.py run_scheduler --astronomic-test
```

This command:
1. Looks up or creates the target "Sirius".
2. Computes its visibility for the current night.
3. Displays Alt/Az coordinates on screen.
