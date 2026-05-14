# Facilities

**Facilities** are adapters that connect TOM Toolkit to the control systems of individual telescopes. RIA TOM includes two custom implementations in addition to those bundled with TOM Toolkit.

## MyObservationFacility

**File**: `iac_tom/facilities/myfacility.py`

Generic example facility for telescopes without a standard OCS system. Useful as a starting point for integrating new telescopes.

**Configured sites**:

| Site | Location |
|------|----------|
| Itagaki | Japan |
| Sierra Remote Observatory | Arizona, USA |

**Behaviour**:

- `submit_observation()`: Prints the observation payload to the console. Does not send to any real telescope.
- `get_observation_status()`: Always returns `["In Progress"]`.
- `validate_observation()`: No validation (accepts any form).

**Form**: Requires `exposure_time` (seconds) and `exposure_count` (number of exposures).

To adapt this facility to a real telescope, override `submit_observation()` with the communication logic specific to your instrument.

## CustomOCSFacility

**File**: `iac_tom/facilities/custom_ocs.py`

Integration with a standard OCS (Observatory Control System) compatible with the LCO protocol. Suitable for observatories that use [OCS](https://github.com/observatorycontrolsystem/observation-portal) as their management system.

**Available forms**:

### Instrument1ObservationForm (Direct imaging)

Additional fields on top of the base OCS form:

| Field | Description |
|-------|-------------|
| `defocus` | Defocus in mm (0–10) |
| `readout_mode` | Detector readout mode |

### SpectrographObservationForm (Spectroscopy)

Additional fields:

| Field | Description |
|-------|-------------|
| `acquisition_mode` | Spectrograph acquisition mode |
| `acquisition_exposure_time` | Acquisition exposure time (s) |
| `guide_star_target` | Guide star target (selectable from the same group) |

## Implementing a new facility

```python
from tom_observations.facilities.base import BaseRoboticObservationFacility

class MyFacility(BaseRoboticObservationFacility):
    name = "MY_FACILITY"
    SITES = {
        "ORM": {
            "sitecode": "orm",
            "latitude": 28.762,
            "longitude": -17.879,
            "elevation": 2396,
        }
    }

    def submit_observation(self, observation_payload):
        # Telescope communication logic
        return [observation_id]

    def get_observation_status(self, observation_id):
        return ["PENDING", "IN_PROGRESS", "COMPLETED"]

    def get_observing_sites(self):
        return self.SITES
```

Register it in `settings.py`:

```python
TOM_FACILITY_CLASSES = [
    "iac_tom.facilities.myfacility.MyObservationFacility",
    "iac_tom.facilities.custom_ocs.CustomOCSFacility",
    "tom_observations.facilities.lco.LCOFacility",
]
```
