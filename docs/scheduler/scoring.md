# Scoring Strategies

Scoring strategies assign a numeric score in [0, 1] to each candidate window. The score determines priority order during time allocation.

## Design pattern

The **Strategy** + **Factory** pattern is used:

```python
factory = ScoringStrategyFactory()
strategy = factory.get_strategy(profile.category)
score = strategy.calculate_score(profile, time, astronomy_engine)
```

## StandardStrategy

**Category**: `STANDARD`

For regular targets with no temporal urgency. Computes a weighted average of five components:

| Component | Weight | Formula |
|-----------|--------|---------|
| Altitude | 1.0 | `alt_deg / 90°` |
| Airmass | 0.5 | `1 - (airmass - 1) / 4` |
| Lunar illumination | 1.0 | `1 - moon_phase` |
| Lunar separation | 1.0 | `moon_sep_deg / 180°` |
| Target priority | 0.5 | `clip(priority, 0, 1)` |

All components are normalised to [0, 1]. The weighted mean produces the final score.

**Example**: A target at zenith (alt=90°, airmass=1.0) with the Moon far away and priority 1.0 will score ≈ 1.0.

## PeriodicStrategy

**Category**: `PERIODIC` (exoplanet transits, eclipsing binary minima)

Adds a Gaussian urgency component centred on the transit midpoint:

**urgency = exp(−(t − t_mid)² / (2 × σ²))**

where **σ = 0.75 × half_window_duration**

- Outside the transit window: score = **0.0** (discarded).
- At transit centre: urgency ≈ **1.0**.
- At transit edges: urgency ≈ **0.5**.

| Component | Weight |
|-----------|--------|
| Urgency (Gaussian) | 3.0 |
| Altitude | 1.0 |
| Airmass | 0.5 |
| Lunar illumination | 0.75 |
| Lunar separation | 0.75 |
| Priority | 0.5 |

The high urgency weight (3.0) ensures transits are prioritised over standard targets.

## TransientStrategy

**Category**: `TRANSIENT` (supernovae, GRBs, novae)

Urgency decays exponentially with a **half-life of 6 hours**:

**urgency = 2^(−hours_since_alert / 6.0)**

| Time since alert | Urgency |
|-----------------|---------|
| 0 h (discovery) | 1.0 |
| 6 h | 0.5 |
| 12 h | 0.25 |
| 24 h | 0.063 |
| 48 h | 0.004 |

| Component | Weight |
|-----------|--------|
| Urgency (exponential) | 3.0 |
| Altitude | 1.0 |
| Airmass | 0.5 |
| Lunar illumination | 0.5 |
| Lunar separation | 0.5 |
| Priority | 0.5 |

After the profile's `fade_date`, the score is **0.0**.

## NonSiderealStrategy

**Category**: `NON_SIDEREAL` (asteroids, comets)

Adapts coordinate computation for objects with proper motion using ephemerides. The target position is updated at each time evaluation. Scoring components are equivalent to `StandardStrategy`.

## Implementing a custom strategy

```python
from scheduler.scoring_strategies.base import ScoringStrategy

class MyCustomStrategy(ScoringStrategy):
    def calculate_score(self, profile, time, astronomy_engine) -> float:
        # Your logic here
        return score  # value in [0.0, 1.0]
```

Register the strategy in `ScoringStrategyFactory` by adding it to the category mapping dictionary.
