# Configuring Telescopes

Before you can run the scheduler, you need to register at least one telescope (also called a **facility** in RIA-TOM). The scheduler uses the telescope's configuration to determine observation feasibility, compute overhead times, and apply mechanical constraints.

---

## Adding a telescope

1. Go to **Telescopes** in the main navigation.
2. Click **Add Telescope**.

The form is divided into several sections described below.

---

## General information

| Field | Description |
|-------|-------------|
| **Name** | A unique name for the telescope (e.g. `IAC80`, `TNG`, `GOTO-N`). |
| **Description** | Optional free-text description of the instrument or programme. |
| **Public** | If checked, all users can see and use this telescope. If unchecked, access is restricted to selected groups. |
| **Groups** | The user groups that can use this telescope. You, as the creator, are the administrator. |

---

## Geographic location

The telescope's coordinates are used for:

- Computing the local sidereal time and target altitude.
- Fetching the weather forecast for that specific location.
- Calculating astronomical twilight times.

| Field | Description |
|-------|-------------|
| **Latitude** | Geographic latitude in decimal degrees (positive = North). |
| **Longitude** | Geographic longitude in decimal degrees (positive = East). |
| **Elevation** | Altitude above sea level in metres. |

!!! tip "Be precise"
    Even a small error in latitude (e.g. 0.1°) can shift twilight times by several minutes. Use the observatory's official coordinates whenever possible.

---

## Mechanical constraints

These fields describe the physical limitations of the telescope mount and its movement:

| Field | Description |
|-------|-------------|
| **Slew rate** | Degrees per second the telescope can move. Used to calculate slew time between targets. |
| **Settle time** | Seconds the telescope needs to stabilise after a slew before the shutter can open. |
| **Mount type** | `ALT-AZ` or `EQUATORIAL`. Equatorial mounts may require a meridian flip. |

---

## Instruments

Each telescope can have one or more instruments. Instruments are used to assign observation frames and calculate readout overhead.

Click **Add Instrument** to define each instrument:

| Field | Description |
|-------|-------------|
| **Instrument name** | Identifier (e.g. `CCD-V`, `CMOS-g`, `EMCCD`). |
| **Readout overhead** | Time in seconds the camera needs to read out after each exposure. |

You can add multiple instruments if the telescope has more than one camera or mode.

---

## Observation limits

These fields define the angular limits within which the telescope can safely point:

| Field | Description |
|-------|-------------|
| **Minimum altitude** | Lowest elevation (degrees) the telescope can point to. This is a hard limit imposed by the mount or dome, independent of the per-target constraint. |
| **Limiting magnitude** | Faintest magnitude observable under typical conditions at this site.|

---

## ASCOM robotic control

If your telescope is controlled by an **ASCOM** driver (common for smaller automated observatories), RIA-TOM can slew and start sequences automatically.

1. Check the **ASCOM Enabled** box.
2. Enter the **ASCOM ProgID** of your telescope driver (e.g. `ASCOM.Celestron.Telescope`, `ASCOM.iOptron.Telescope`).

Once configured, the **Start Sequence** button will appear in the schedule viewer, allowing automatic execution of the night's plan. See [Viewing Schedules — Robotic execution](schedules.md#robotic-execution-ascom) for details.

!!! note "ASCOM availability"
    ASCOM is a Windows-only framework. This feature requires RIA-TOM to be running on a Windows host, or on a machine with network access to an ASCOM Remote server.

---

## Editing and deleting telescopes

From the **Telescopes** list, click on any telescope to open its detail view. Use the **Edit** and **Delete** buttons to modify or remove it.

!!! warning "Deleting a telescope"
    Removing a telescope will also delete all associated **FacilitySettings** and may affect existing **SchedulingProfiles** that reference it. Scheduled windows assigned to that facility will lose their facility assignment.

---

## Sharing telescopes between groups

If your observatory has multiple observing teams, you can share a telescope with specific groups:

1. Edit the telescope.
2. In the **Groups** field, add the groups that should have access.
3. Uncheck **Public** if you want to restrict access only to those groups.

Members of those groups will be able to assign the telescope to their targets and run the scheduler for it.
