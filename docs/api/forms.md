# Forms API Reference

## SchedulingProfileForm

```python
from scheduler.forms import SchedulingProfileForm
```

ModelForm for creating and editing scheduling profiles.

**Visible fields**:

| Field | Widget | Description |
|-------|--------|-------------|
| `category` | Select | Target type |
| `priority` | NumberInput | Priority multiplier |
| `is_active` | CheckboxInput | Active in scheduler |
| `min_seeing` | NumberInput | Minimum seeing (") |
| `night_type` | Select | Required night type |
| `transparency_type` | Select | Required transparency |
| `min_altitude` | NumberInput | Minimum altitude (°) |
| `max_airmass` | NumberInput | Maximum airmass |
| `min_moon_distance` | NumberInput | Minimum lunar distance (°) |
| `max_moon_illumination` | NumberInput | Maximum lunar illumination |
| `facility` | Select | Assigned telescope |
| `transit_start` | DateTimeInput | Transit start (PERIODIC) |
| `transit_end` | DateTimeInput | Transit end (PERIODIC) |
| `fade_date` | DateTimeInput | Fade-out date (TRANSIENT) |

**Custom logic**:

- The `facility` queryset is filtered by user: shows public facilities and those managed or shared with the user.
- If only one facility is available, it is pre-selected automatically.

---

## ObservationFramesFormSet

```python
from scheduler.forms import ObservationFramesFormSet
```

Inline formset for editing multiple exposure frames of a profile.

Allows adding, editing, and deleting frames. All widgets are styled for Bootstrap.

**Fields per frame**:

| Field | Widget | Description |
|-------|--------|-------------|
| `filter_name` | TextInput | Filter name (e.g. `R`, `Ha`) |
| `exposure_time` | NumberInput | Exposure duration (s) |
| `repetitions` | NumberInput | Number of repetitions |
| `dithering_enabled` | CheckboxInput | Enable dithering |
| `ra_offset` | NumberInput | RA offset (arcsec) |
| `dec_offset` | NumberInput | Dec offset (arcsec) |

---

## GroupConstraintsForm

```python
from scheduler.forms import GroupConstraintsForm
```

Form for bulk-updating constraints for all targets in a group.

All fields are optional. Only explicitly filled fields are applied.

**Fields**: Same as `SchedulingProfileForm` but all with `required=False`.

---

## FacilitySettingsForm

```python
from scheduler.forms import FacilitySettingsForm
```

ModelForm for creating and editing telescope/facility records.

**Main fields**:

| Field | Widget | Description |
|-------|--------|-------------|
| `name` | TextInput | Unique telescope name |
| `description` | Textarea | Free-text description |
| `is_public` | CheckboxInput | Public access |
| `shared_with` | CheckboxSelectMultiple | Groups with access |
| `latitude` / `longitude` / `elevation` | NumberInput | Observatory coordinates |
| `slew_rate` | NumberInput | Slew speed (°/s) |
| `settle_time` | NumberInput | Settle time (s) |
| `readout_overhead` | NumberInput | Readout overhead (s) |
| `horizon_limit` | NumberInput | Horizon limit (°) |
| `limiting_magnitude` | NumberInput | Limiting magnitude |
| `ascom_enabled` | CheckboxInput | Enable ASCOM control |
| `ascom_driver` | TextInput | ASCOM driver ProgID |
