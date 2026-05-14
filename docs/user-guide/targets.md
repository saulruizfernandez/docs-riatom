# Managing Targets

Targets are the astronomical objects you wish to observe. RIA-TOM stores them in a database and supports four categories, each with its own scheduling behaviour.

---

## Target types

RIA-TOM inherits target types from the TOM Toolkit and assigns each a **scheduler category**:

| TOM type | Scheduler category | Description |
|----------|--------------------|-------------|
| Sidereal | **STANDARD** | Stars, galaxies, nebulae, clusters. No time urgency. |
| Sidereal | **TRANSIENT** | Supernovae, GRBs, fast transients. High and decaying urgency. |
| Sidereal | **PERIODIC** | Transiting exoplanets, eclipsing binaries. Strict time window required. |
| Non-Sidereal | **NON_SIDEREAL** | Asteroids, comets. Position computed from orbital ephemerides. |

The category is set when configuring the target's scheduling profile (see [Defining Constraints](constraints.md)). It determines how the scheduler scores and prioritises the target.

---

## Adding targets

There are three ways to add targets to RIA-TOM: catalog search, manual coordinate entry, and automated alert brokers.

### Method 1 — Catalog search

The quickest way to add a well-known object.

1. Go to **Targets → Create Targets → Catalog Search**.
2. Select the **Service** from the dropdown:

    | Service | Best for |
    |---------|----------|
    | **Simbad** | Stars, galaxies, nebulae, clusters — resolves common names and aliases |
    | **NED** | Extragalactic objects (galaxies, quasars, AGN) — includes redshifts |
    | **JPL Horizons** | Solar system bodies — planets, comets, asteroids by Horizons ID |
    | **TNS** | Transients (supernovae, AT events) — enter the IAU designation **without prefix** (e.g. `2024abc`) |

3. Type the object name and click **Search**. The service will return the coordinates and metadata.
4. Optionally fill in any extra fields, then click **Submit**.

!!! example "Adding the Orion Nebula"
    Select **Simbad**, type `M42`, and click **Search**. RIA-TOM will resolve it to RA 83.822°, Dec −5.391° and pre-fill the form. Click **Submit** to save it to your database.

### Method 2 — Manual coordinate entry

Use this method when the object is not in any catalog or when you have precise custom coordinates.

1. Go to **Targets → Create Targets → Create a Target**.
2. Select the target type:

#### Sidereal targets

Fill in:

- **Name** — a unique identifier.
- **Right Ascension (RA)** — in decimal degrees or `HH:MM:SS.sss` format.
- **Declination (Dec)** — in decimal degrees or `±DD:MM:SS.ss` format.
- Any optional fields (epoch, proper motion, parallax, magnitude, etc.).

#### Non-Sidereal targets

Fill in the orbital elements:

| Field | Description |
|-------|-------------|
| **Orbital Element Scheme** | MPC or JPL format |
| **Epoch of Elements** | Reference epoch (MJD) |
| **Eccentricity** | Orbit eccentricity (0–1 for elliptic) |
| **Inclination** | Inclination to the ecliptic (degrees) |
| **Longitude of Ascending Node** | Ω (degrees) |
| **Argument of Perihelion** | ω (degrees) |
| **Semi-major axis / Perihelion distance** | Depending on scheme |

3. Click **Submit**.

!!! note "Scheduling profile"
    After creating a target, it has no scheduling constraints yet — the scheduler will ignore it until you configure its **Scheduling Profile**. See [Defining Constraints](constraints.md) to set it up.

### Method 3 — Alert brokers (periodic and transient targets)

Alert brokers provide real-time streams of newly discovered transient and periodic objects. This is the recommended way to add **PERIODIC** and **TRANSIENT** category targets.

RIA-TOM integrates with the following brokers:

| Broker | Source | Auto-detected categories |
|--------|--------|--------------------------|
| **ALeRCE** | ZTF alerts, ML classification (`lc_classifier_top`) | TRANSIENT, PERIODIC |
| **Gaia** | Gaia Science Alerts | TRANSIENT |
| **Lasair** | Processed ZTF alerts | TRANSIENT |
| **TNS** | Transient Name Server (SNe, AT events) | TRANSIENT |

#### Step-by-step: adding targets from ALeRCE

1. Go to **Alerts** in the main navigation.
2. Click **Create Query**.
3. Fill in:
    - **Name** — a label for this query (e.g. `Periodic survey 2025`).
    - **Broker** — select `ALeRCE`.
    - **Target type** — choose the category you want to retrieve (e.g. *Periodic*).
4. Click **Submit**, then **Run**.
5. A results table will appear listing all matching objects returned by the server.
6. Select the targets you are interested in using the **checkboxes**.
7. Click **Create Targets**.

RIA-TOM will:

- Save each selected object as a target in the database.
- Automatically assign the correct **scheduler category** based on the broker's classification.
- Create an empty **SchedulingProfile** ready to configure.
- Link the target to your user group.

!!! tip "Filtering results"
    Before clicking *Create Targets*, use the table filters to narrow down the list by magnitude, classification probability, or other available fields.

---

## Extra fields

### Redshift

RIA-TOM adds a custom `redshift` field to sidereal targets. It is available in the target detail interface and can be used to record the spectroscopic redshift of galaxies and quasars.

---

## Viewing all targets

Go to **Targets** in the main navigation to see the full list of targets in your database. You can:

- Filter by name, type, or group.
- Click any target to open its detail view.
- Use **Target Groups** to organise targets by project or observing programme.
