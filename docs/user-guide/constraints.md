# Defining Constraints & Observation Frames

Once a target is in the database, you need to configure its **Scheduling Profile** — the set of conditions under which it can be observed and the frames the telescope should acquire.

The scheduler evaluates constraints before scoring a candidate window. If a constraint is not satisfied, the window is discarded entirely.

---

## Accessing the scheduling profile

There are two ways to reach a target's scheduling profile:

- From the **Scheduler → Manage your targets** view, find the target, and click **Config**.
- Alternatively, open the target's detail page and go to the **Scheduling Profile** tab.

---

## Step 1 — General settings

### Facility and instrument

| Field | Description |
|-------|-------------|
| **Facility** | The telescope assigned to observe this target. Only telescopes registered in your account are shown. |
| **Instrument** | The camera or instrument on that telescope. Drives the readout overhead calculation. |

!!! warning "Facility required"
    If no facility is assigned, the scheduler will skip this target and display a warning in the profile table.

### Scientific priority

The **Priority** field (integer, higher = more important) breaks ties between targets that are otherwise equally feasible at the same time. Targets with a higher priority are preferred when the scheduler must choose.

### Active / Inactive toggle

The **Active** checkbox controls whether the scheduler considers this target at all. Inactive targets are stored in the database but invisible to the scheduling engine. This is useful to temporarily suspend a target without deleting its configuration.

---

## Step 2 — Category

Select the **category** that describes the scientific nature of the target:

| Category | Typical objects | Scheduling behaviour |
|----------|-----------------|----------------------|
| **STANDARD** | Stars, galaxies, clusters, nebulae | Scored by airmass and priority. No time urgency. |
| **TRANSIENT** | Supernovae, GRBs, novae | Score decays exponentially with time since discovery. |
| **PERIODIC** | Transiting exoplanets, eclipsing binaries | Only schedulable within a strict transit/eclipse window. |
| **NON_SIDEREAL** | Comets, asteroids | Position interpolated from ephemerides at scheduling time. |

Choosing the correct category is important: it controls the scoring formula the scheduler applies to the target.

---

## Step 3 — Observability constraints

These constraints define the minimum conditions required for a valid observation.

### Minimum altitude

**Default: 30°**

The target must be above this elevation above the horizon. Observing at low altitudes increases airmass and degrades image quality.

| Site type | Recommended value |
|-----------|------------------|
| High-altitude, good horizon | 25–30° |
| Seeing-sensitive science | 35–40° |
| Extended objects / low priority | 20–25° |

### Maximum airmass

**Default: 2.5**

Airmass X = 1 / sin(altitude). At the zenith X = 1.0. Recommended limits by science case:

| Science requirement | Max airmass |
|--------------------|------------|
| High-precision photometry | 1.5 |
| Standard imaging | 2.0 |
| Low-priority monitoring | 2.5 |

!!! note "Altitude vs. airmass"
    Setting both minimum altitude and maximum airmass is redundant — they constrain the same quantity from different angles. It is fine to set only one.

### Minimum seeing

**Default: 0.6"**

The minimum atmospheric seeing (in arcseconds) required. If the forecast seeing is *worse* (larger) than this value, the window is discarded. Leave blank or set to `0` to disable weather-based seeing filtering.

### Minimum lunar distance

**Default: 20°**

Minimum angular separation between the target and the Moon at the scheduled time.

| Observation type | Recommended value |
|-----------------|------------------|
| Faint spectroscopy | 30–45° |
| Standard photometry | 20–30° |
| Bright or nearby objects | 10–15° |

### Maximum lunar illumination

**Default: 0.85 (85%)**

Fraction of the lunar disc that is illuminated [0.0–1.0]. This constraint rejects scheduling during periods when the Moon is too bright.

| Night type | Illumination |
|-----------|-------------|
| Dark | < 25% |
| Grey | 25–75% |
| Bright | > 75% |

---

## Step 4 — Sky condition constraints

### Night type

Restricts scheduling to nights with a specific sky background:

| Value | Description |
|-------|-------------|
| `dark` | Only dark nights (Moon illumination < 25%) |
| `gray` | Dark or grey nights (illumination < 75%) |
| `bright` | Any night, regardless of Moon |
| `dm` | *Don't mind* — no restriction |

### Transparency

Minimum required atmospheric transparency:

| Value | Description |
|-------|-------------|
| `photometric` | Photometric sky — no clouds, constant extinction |
| `clear` | Clear sky |
| `thin` | Thin cirrus tolerable |
| `thick` | Thick clouds tolerable (e.g. for spectroscopy of bright targets) |
| `dm` | *Don't mind* — no restriction |

---

## Step 5 — Category-specific fields

### PERIODIC — Transit window

Targets of category PERIODIC require you to define the exact transit or eclipse window:

| Field | Description |
|-------|-------------|
| **Transit start** | UTC datetime of the event beginning |
| **Transit end** | UTC datetime of the event end |

The scheduler ignores this target outside this window. Near the transit centre, the score is boosted to maximise the probability of scheduling it during ingress, maximum, and egress.

!!! example "Exoplanet transit"
    If transit ingress is at 22:15 UTC and egress at 01:35 UTC, set `transit_start = 2025-06-12T22:15:00Z` and `transit_end = 2025-06-13T01:35:00Z`. The scheduler will only consider windows that overlap this interval.

### TRANSIENT — Fade date

Targets of category TRANSIENT can have a **Fade date** — a UTC datetime after which the object is no longer scientifically relevant. The scheduler will completely discard windows after this date.

Additionally, urgency decays **exponentially** from the alert/discovery date: the score halves every 6 hours. This ensures that newly discovered transients are observed as quickly as possible.

---

## Step 6 — Observation frames

Frames define the **exposure sequences** the telescope will execute for this target. They are used to calculate the total integration time and, together with the telescope's slew rate, settle time, and readout overhead, determine the total time slot that needs to be allocated.

Click **Add Frame** to define each exposure set:

| Field | Description |
|-------|-------------|
| **Filter** | Filter name (e.g. `B`, `V`, `R`, `g`, `r`, `i`, `Ha`). Must match the instrument's filter wheel. |
| **Exposure time** | Duration of a single exposure in seconds. |
| **Repetitions** | Number of exposures in this filter. |
| **Dithering** | Enable dithering between exposures (recommended for mosaics and faint targets). |
| **RA offset** | Dither offset in RA (arcseconds). |
| **Dec offset** | Dither offset in Dec (arcseconds). |

You can add multiple frame sets to observe the target in several filters within the same visit.

!!! tip "Total time calculation"
    Total allocated time = Σ (exposure_time × repetitions) + readout_overhead × total_exposures + slew_time + settle_time

    This is computed automatically by the scheduler when allocating windows.

---

## Bulk constraint update by group

If you have many targets with identical constraints (e.g. all targets in a monitoring programme), you can update them all at once:

1. Go to **Manage your targets**.
2. Select the group.
3. Click on the project id.
4. Fill in only the fields you want to update — blank fields are ignored.
5. Click **Save**.

!!! warning "Overwrite behaviour"
    Bulk update **overwrites** the individual constraints of every target in the group, even those that had different values. Use with care.

