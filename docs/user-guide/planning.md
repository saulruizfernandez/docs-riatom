# Observation Planning

The **Scheduler** tab is the heart of RIA-TOM. It brings together your targets, telescope configuration, and weather data to produce an optimised observation plan. The interface is deliberately divided into three clear steps:

1. **Manage your targets** — configure what to observe and how.
2. **Plan your nights** — define the observation windows.
3. **Check your schedule** — run the scheduler and review results.

---

## Getting to the Scheduler

Click **Scheduler** in the main navigation. You will see the three-panel layout corresponding to the three steps above.

---

## Quick import via CSV

Before going through the manual steps, note that there is a shortcut: **Upload Observation Block (CSV)**. This button is at the top of the Scheduler page.

With a CSV file you can:

- Import targets that are **not yet in the database** — they will be created automatically.
- Define observation blocks without having to configure each target individually.
- Skip manually creating projects or scheduling profiles — everything is built from the CSV.

## CSV Format Specification

### CSV Structure

The file must be a standard comma-separated values (CSV) file including a header row with the following exact names:

| Column | Type | Description |
| :--- | :--- | :--- |
| **`NN`** | `int` | **Burst Multiplier:** The number of consecutive exposures to be taken at each specific position (row). |
| **`prj_id`** | `string` | **Project ID:** Unique identifier for the astronomical campaign or project. |
| **`object`** | `string` | **Object Name:** The name of the target being observed (e.g., M31, Orion). |
| **`tray`** | `string` | **Tray/Config:** Defines the filter, instrument tray, or specific hardware setup used. |
| **`exp_time`** | `float` | **Exposure Time:** The duration of a single exposure in seconds. |
| **`ra_azimut`** | `float` | **Base RA/Azimuth:** The base horizontal coordinate for the telescope pointing. |
| **`dec_elevation`** | `float` | **Base DEC/Elevation:** The base vertical coordinate for the telescope pointing. |
| **`ra_az_offset`** | `float` | **RA Offset:** The relative horizontal displacement from the base coordinate for dithering. |
| **`dec_el_offset`** | `float` | **DEC Offset:** The relative vertical displacement from the base coordinate for dithering. |

---

### Data Example
```csv
NN,prj_id,object,tray,exp_time,ra_azimut,dec_elevation,ra_az_offset,dec_el_offset
1,PRJ-01,M42,Filter_R,120.0,83.82,-5.39,0.0,0.0
1,PRJ-01,M42,Filter_R,120.0,83.82,-5.39,0.1,0.0
1,PRJ-01,M42,Filter_R,120.0,83.82,-5.39,0.0,0.1
1,PRJ-01,M42,Filter_R,120.0,83.82,-5.39,0.1,0.1
```

After uploading, you can review and edit the **general project constraints** before the import is finalised. This is the fastest way to load a large observing programme.

---

## Step 1 — Manage your targets

This panel lets you add or remove targets from the scheduler, and edit their individual configurations.

### Adding a target to the scheduler

1. Type the target name in the **search bar** (it searches your database by name).
2. Click the target in the results dropdown to add it to the scheduler.
3. A configuration panel will appear immediately for that target. Fill in:

    | Field | Description |
    |-------|-------------|
    | **Facility** | The telescope to use for this target |
    | **Instrument** | The camera on that telescope |
    | **Priority** | Scientific priority (higher = preferred) |
    | **Active** | Whether the scheduler should consider this target |

4. Set the [observability constraints](constraints.md) and the **target category**.
5. Add one or more **observation frame sets** (filter, exposure time, repetitions, dithering).

### Removing a target from the scheduler

Use the same search bar to find the target and click **Remove**. This does not delete the target from the database — it only removes it from the active scheduling pool.

### Editing an existing profile

Click **Config** on any row in the profile table, or search for the target and click **Config** in the results.

### Observation blocks

An **Observation Block** is a group of targets that will be observed together as a single scheduled unit. The block is treated as an atomic entity: if the scheduler allocates it, all its targets are observed sequentially.

**Creating a block manually:**

1. In the *Manage your targets* panel, click **New Block**.
2. Search for targets and add them to the block one by one.

!!! note "Auto-created blocks"
    Blocks are created automatically when you import via CSV.

---

## Step 2 — Plan your nights

Before running the scheduler, you need to define which night(s) you want to plan. Click **Add Observation Window** in the *Plan your nights* panel.

### Creating a night window

| Field | Description |
|-------|-------------|
| **Date** | The calendar date of the night. |
| **Automatic twilight** | The scheduler computes astronomical twilight start/end times for your observatory location. |
| **Comet night** | Check this if you are observing comets or any other object that are only visible at twilight, or any other case where you do not want to restrict to astronomical night. |
| **Time exclusions** | Optional intervals within the night to exclude (e.g. instrument calibrations, dome maintenance windows). |

Click **Save**. The selected date will appear highlighted with a **blue background** on the calendar.

### Viewing and managing night windows

Each configured night is shown on the calendar. You can:

- Edit a night window to add time exclusions.
- Delete a night window to remove it from the plan.

---

## Step 3 — Check your schedule

The final panel before running the scheduler. Here you configure two important options:

### Category filters

Use the **category toggles** to include or exclude entire target categories from the scheduling run. For example, you might want to:

- Disable NON_SIDEREAL if you have no comets tonight.
- Focus only on TRANSIENT if there is a new supernova that needs immediate follow-up.

### Category priorities (drag and drop)

Drag the category cards to set the relative priority order. Categories listed higher will be preferred over lower ones when the scheduler must choose between competing targets.

### Weather forecast toggle

The weather forecast is used as an **additional constraint** on the scheduler:

- If the forecast for a given time slot does not meet a target's `min_seeing` or `transparency_type`, the window is discarded.
- **This only applies when the planned night is tonight.** For future nights, the weather constraint is automatically ignored (no reliable forecast).
- You can **deactivate** the weather forecast toggle if the weather station or forecast API is giving unreliable data.

---

## Running the scheduler

Once your night windows, targets, and settings are ready:

1. **Click on the desired day** in the calendar.
2. The scheduler will run automatically and compute the optimal allocation.
3. When it finishes, it shows the results.

If the schedule for a night **already exists in the database** (because you have previously run it, or made manual edits), clicking the day will **load the saved plan** instead of re-running the scheduler. This preserves your manual adjustments.

To force a fresh run, click the **Auto-run Schedule** button and confirm. This deletes the current plan for that night and runs the scheduler from scratch.

---

## Understanding the scheduling algorithm

The scheduler uses a **greedy allocation** strategy:

1. For each time slot during the night, it evaluates all active targets.
2. It applies all constraints (altitude, airmass, moon, weather, category filters).
3. It scores each feasible target using the category-specific formula.
4. It allocates the highest-scoring target to that time slot.
5. It repeats until all slots are filled or no more feasible targets remain.
