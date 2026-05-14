# Viewing and Exporting Schedules

After the scheduler runs, RIA-TOM presents the results in three complementary visualisations: a **Gantt chart**, **visibility curves**, and a **detailed target overview**. You can also export the schedule as a CSV file or — if your telescope supports it — execute it automatically.

---

## Gantt chart

The Gantt chart is the primary view of your night plan. It appears automatically after the scheduler finishes.

Each **horizontal bar** represents one scheduled observation. The bar shows:

- **Target name** (or observation block name)
- **Start and end time** of the observation
- **Score** as a percentage (higher = more optimal)
- **Telescope** it is assigned to (colour-coded by facility)

Observation blocks are shown as a single grouped bar, with the individual targets listed inside it.

### Telescope filter

If you have multiple telescopes configured, use the **telescope selector** above the chart to show or hide individual facilities. This is useful for focusing on a single instrument.

### Modifying the schedule manually

The Gantt chart is fully interactive. You can adjust the schedule after the automated run:

**Moving an observation:**

1. Click on the bar you want to move.
2. Drag it to the new time slot.

**Changing duration:**

1. Click on the bar.
2. Drag the right edge to extend or shorten the observation window.

!!! warning "Schedule modified indicator"
    After any manual change, a **"Schedule not up to date"** warning will appear. This means your edits have been saved to the database, but they deviate from the automatically computed optimal plan. The next time you click on this day in the calendar, RIA-TOM will **load your edited version** from the database rather than re-running the scheduler.

**Resetting the schedule:**

To discard all manual changes and re-run the scheduler from scratch, click the **Reset Schedule** button and confirm. This permanently deletes the current plan for that night.

---

## Visibility curves

Below the Gantt chart you will find the **visibility curves** panel. This shows the altitude of each scheduled target throughout the entire night.

The time interval during which a target is **scheduled** is highlighted on its curve, making it easy to verify that:

- The target is above your minimum altitude during its scheduled slot.
- There are no gaps in altitude that would cause the telescope to lose it.
- The Moon is sufficiently separated.

### Controls

| Action | Effect |
|--------|--------|
| **Hover** over the curve | Shows the precise altitude and scheduled time at that point |
| **Single click** on a legend entry | Hides or shows that target's curve |
| **Double-click** on a legend entry | Isolates that target (all others hidden) |
| **Double-click** again on the legend entry | Returns to normal view (all targets shown) |
| **Double-click** on the graph background | Switches between full-night view and scheduled-window zoom view |

---

## Detailed schedule overview

The third section shows a **card for each scheduled target**, containing:

| Information | Description |
|-------------|-------------|
| **Target image** | Sky survey image centred on the target. Use the survey dropdown to switch between DSS, SDSS, 2MASS, etc. |
| **Filters used** | List of filters in the observation frame sets |
| **Total exposures** | Number of individual exposures per filter |
| **Dithering parameters** | RA/Dec offsets if dithering is enabled |
| **Altitude graph** | Mini altitude-over-night chart for that target |
| **Scheduled time** | Precise start and end time (UTC) |

This section is ideal for a final review before the night starts, or for printing a hard-copy observation checklist.

---

## Exporting the schedule

Click the **Export CSV** button at the top of the schedule view to download the full night plan as a CSV file.

| Column | Description |
|--------|-------------|
| `target` | Target name |
| `ra` | Right ascension (decimal degrees) |
| `dec` | Declination (decimal degrees) |
| `start_time` | Window start (UTC ISO 8601) |
| `end_time` | Window end (UTC ISO 8601) |
| `score` | Scheduler score [0–1] |
| `facility` | Assigned telescope name |
| `instrument` | Assigned instrument name |
| `filter` | Filters used |
| `exposure_time` | Exposure time(s) per filter (seconds) |
| `repetitions` | Number of exposures per filter |

The CSV is compatible with common spreadsheet applications and can be imported into observatory control systems that accept tabular formats.

---

## Robotic execution (ASCOM)

If your telescope is connected via an **ASCOM driver** (see [Configuring Telescopes](telescopes.md#ascom-robotic-control)), you can execute the schedule automatically.

### Starting a sequence

1. In the Nightly Planner section, click the **Start Sequence** button (only visible if an ASCOM-enabled telescope is configured).
2. A dialog will appear listing all ASCOM-enabled telescopes.
3. Select the telescope you want to control.
4. Click **Start Sequence**.