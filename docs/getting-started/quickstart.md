# Quick Start

This guide walks through the basic workflow: registering a target, configuring its constraints, and generating an observation plan.

## 1. Access the application

Navigate to `http://localhost:80/` and log in with the superuser created during installation (`user:admin, password:admin1234`). If you are an IAC user, just log in with your credentials, as RIA-TOM will create automatically an account for you.

The home page shows:

- **Night astronomical status**: night type (dark/grey/bright) based on lunar illumination.
- **Weather forecast**: predicted conditions for the coming hours at the configured observatory.
- **Recent targets**: list of the most recently added targets.

## 2. Create a Telescope

Before scheduling observations you need to register at least one telescope.

1. Go to **Telescopes → Add telescope**.
2. Fill in the required fields:
   - **Name**: telescope identifier (e.g. `IAC80`)
   - **Latitude / Longitude / Elevation**: geographic coordinates of the observatory.
   - **Horizon limit**: minimum observable altitude (°), typically 30°.
3. Save the telescope.

## 3. Add a Target

1. Go to **Targets → Create Targets → Catalog Search**.
2. Enter the name of the target and press on the "Search" button.
3. Save the target by clicking on the "Submit" button.

## 4. Configure the Scheduling Profile

Each target has a **Scheduling Profile** that defines when and how it should be observed.

1. From the target page, click **Add to Scheduler**.
2. Configure:
    - **Category**: `STANDARD` for regular targets.
    - **Facility**: select the telescope created in step 2.
    - **Constraints**: minimum altitude, maximum airmass, minimum lunar distance, etc.
3. In the **Observation Frames** section, add at least one frame:
    - **Filter**: filter name (e.g. `R`, `V`, `Ha`).
    - **Exposure time**: exposure duration in seconds.
    - **Repetitions**: number of exposures.
4. Save the configuration.

## 5. Generate the Observation Plan

1. Go to **Scheduler**.
2. Add an **observation Window** in the calendar.
3. Click on the observation window created to run the scheduler.
4. The scheduler will compute visibility windows, score each target, and produce an optimized schedule.
5. The result is displayed on the interactive calendar as time blocks.

## 6. Export the Schedule

From the Timeline view you can export the generated plan to CSV:

**Scheduler → 3.Check your schedule → Export CSV**
