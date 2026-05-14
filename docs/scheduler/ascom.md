# ASCOM Controller

The ASCOM module enables direct control of robotic telescopes compatible with the [ASCOM](https://ascom-standards.org/) standard from RIA TOM.

!!! warning "Windows only"
    ASCOM uses Windows COM (Component Object Model). This module **only works on Windows** with ASCOM Platform installed.

## Requirements

- Windows 10/11
- [ASCOM Platform 6.6+](https://ascom-standards.org/Downloads/Index.htm)
- Telescope ASCOM driver installed
- `win32com` library (`pip install pywin32`)

## Configuration

In `FacilitySettings`, enable ASCOM control:

- **`ascom_enabled`**: `True`
- **`ascom_driver`**: driver ProgID, e.g. `"ASCOM.Simulator.Telescope"` for the simulator, or `"ASCOM.OptecGemini.Telescope"` for a real Gemini mount.

## Telescope states

The controller maintains a global state accessible from the API:

| State | Description |
|-------|-------------|
| `IDLE` | No active session |
| `CONNECTING` | Connecting to the ASCOM driver |
| `CONNECTED` | Connected, waiting to start |
| `SLEWING` | Slewing towards the target |
| `TRACKING` | Tracking the target (observing) |
| `ERROR` | Connection or movement error |
| `STOPPED` | Manually stopped |

## Control API

### Start sequence

`POST /scheduler/api/ascom/start/`

Starts the execution of scheduled windows. The controller:

1. Connects to the specified ASCOM driver.
2. Waits until the `start_time` of the first window.
3. Slews to the target (`SlewToCoordinatesAsync`).
4. Enables tracking.
5. Repeats for each window in chronological order.

### Stop sequence

`POST /scheduler/api/ascom/stop/`

Stops the sequence at the current point. The telescope stops tracking the current target.

### Query status

`GET /scheduler/api/ascom/status/`

Returns the current state in JSON:

```json
{
    "state": "SLEWING",
    "current_target": "SN2024abc",
    "message": "Slewing to RA=10.5, Dec=+45.2",
    "progress": 3,
    "total": 8
}
```

## Internal implementation

The `_run_sequence(driver_name, windows)` function runs in a **background thread** to avoid blocking the Django server. State is updated in a thread-safe manner via a lock:

```python
with _status_lock:
    _status["state"] = TelescopeState.SLEWING
    _status["current_target"] = target.name
```

## Manual usage example

```python
from scheduler.ascom_controller import _run_sequence
from scheduler.models import ScheduledWindow
import threading

windows = ScheduledWindow.objects.filter(
    facility__ascom_enabled=True,
    start_time__gte=timezone.now()
).order_by("start_time")

thread = threading.Thread(
    target=_run_sequence,
    args=("ASCOM.Simulator.Telescope", list(windows))
)
thread.start()
```
