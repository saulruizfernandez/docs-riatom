# Data Flow

The complete planning cycle follows these steps:

## 1. Target Ingestion

Targets can arrive through four paths:

```mermaid
flowchart TD
    A["Alert\n(ALeRCE · Gaia · Lasair · TNS)"] --> B["CreateTargetFromAlertView"]
    B --> B1["Infers category\n(TRANSIENT, PERIODIC)"]
    B --> B2["Auto-creates SchedulingProfile"]

    C["Manual creation"] --> D["TOM Toolkit — Add Target"]
    D --> D1["SchedulingProfile\ncreated manually"]

    E["Upload CSV"] --> F["Massive SchedulingProfile creation"]
    H["Via API/REST"] --> F
```

## 2. Constraint Configuration

The astronomer configures the target's `SchedulingProfile`:

| Field | Description |
|-------|-------------|
| `facility` | Telescope to use |
| `category` | STANDARD / TRANSIENT / PERIODIC / NON-SIDEREAL |
| `min_altitude`, `max_airmass` | Observability constraints |
| `min_moon_distance`, `max_moon_illumination` | Lunar constraints |
| `min_seeing`, `transparency_type` | Sky quality constraints |
| `ObservationFrames` | Filters, exposures, dithering |
| `priority` | Urgency multiplier |

## 3. Scheduler Execution

When **Scheduler** is executed, `SchedulerCore` runs the following pipeline:

```mermaid
flowchart TD
    subgraph GCW["generate_candidate_windows()"]
        subgraph LOOP["↻  For each active SchedulingProfile"]
            B["AstronomyEngine.get_observable_blocks()\nCompute observable time blocks for the night"]
            C["Filter intervals above the horizon"]
            D["Apply constraints\nairmass · moon · weather · night_type"]
            E["ScoringStrategy.calculate_score()\nScore each valid interval"]
            F(["CandidateWindow\nwith average score"])
            B --> C --> D --> E --> F
        end
    end

    subgraph ALLOC["GreedyAllocationStrategy.allocate(candidates)"]
        G["Sort candidates by score — descending"]
        H["Assign non-overlapping slots per facility"]
        G --> H
    end

    GCW --> ALLOC
    ALLOC --> K[("Save ScheduledWindow records to DB")]
```

## 4. Visualisation

Once scheduling is complete, `TimelineView` loads the generated `ScheduledWindow` records and serves them to the frontend through three JSON endpoints:

| Endpoint | Description |
|---|---|
| `GET /scheduler/api/events/` | Scheduled windows for the FullCalendar view |
| `GET /scheduler/api/gantt/` | Observation distribution per facility across the night |
| `GET /scheduler/api/nightly/` | Tonight's targets with scores and visibility windows |

## 5. Export / Execution

Once the schedule is ready, two output paths are available:

- **CSV export** — Downloads a file with the complete schedule (target, start, end, score, frames).
- **ASCOM Controller** — Executes the sequence robotically, slewing the telescope at each scheduled time.
