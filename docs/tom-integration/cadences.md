# Cadences

**Cadences** in TOM Toolkit are re-observation strategies that determine when to observe a target again based on its observation history.

## Configured strategies

```python
TOM_CADENCE_STRATEGIES = [
    "tom_observations.cadences.retry_failed_observations.RetryFailedObservations",
    "tom_observations.cadences.resume_cadence_after_failure.ResumeCadenceAfterFailure",
    "iac_tom.cadences.mycadence.MyCadenceStrategy",
]
```

## Strategies included in TOM Toolkit

### RetryFailedObservations

Automatically retries observations that have failed. Useful for recovering from transient technical failures (bad weather, telescope fault).

### ResumeCadenceAfterFailure

Resumes the cadence pattern after a failure, computing the next observation from the last successful state rather than the last failed attempt.

## MyCadenceStrategy (customisable)

**File**: `iac_tom/cadences/mycadence.py`

Empty placeholder for implementing a custom cadence strategy for the RIA.

```python
from tom_observations.cadences import CadenceStrategy

class MyCadenceStrategy(CadenceStrategy):
    name = "My Cadence Strategy"
    description = "Custom cadence for RIA"

    def run(self):
        # Implement cadence logic
        pass
```

## Example: time-since-last-observation cadence

A typical implementation for transient follow-up might be:

```python
def run(self):
    from datetime import timedelta
    from django.utils import timezone

    for observation_record in self.observation_group.observation_records.all():
        last_obs = observation_record.created
        interval = timedelta(hours=self.parameters.get("interval_hours", 24))
        if timezone.now() - last_obs >= interval:
            # Submit new observation
            self.submit_observation(observation_record.target)
```

## Relationship with the Scheduler

TOM Toolkit cadences and the Scheduler module are complementary mechanisms:

- **Cadences** manage long-term re-observation (days, weeks).
- The **Scheduler** optimises the agenda for a specific night.

In the RIA TOM workflow, the Scheduler can consider the `last_scheduled` field of `SchedulingProfile` to increase the priority of targets that have not been observed for a long time.
