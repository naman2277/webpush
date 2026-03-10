# Overload Intelligence Model (v1)

This document defines a practical formula to classify user occupancy.

## Inputs

For each user `u` in time window `w` (e.g., last 14 days):

- `available_hours(u,w)`
- `estimated_hours_assigned(u,w)`
- `actual_productive_hours(u,w)`
- `blocked_hours(u,w)`
- `meeting_hours(u,w)`
- `task_switch_count(u,w)`
- `active_task_count(u,w)`

## Derived Scores (0-100)

- `committed_load = min(100, 100 * estimated_hours_assigned / max(1, available_hours))`
- `execution_load = min(100, 100 * actual_productive_hours / max(1, available_hours))`
- `blocked_ratio = min(100, 100 * blocked_hours / max(1, estimated_hours_assigned))`
- `interruption = min(100, 100 * (meeting_hours / max(1, available_hours)))`
- `fragmentation = min(100, 100 * (task_switch_count / max(1, active_task_count * 4)))`

## Classification Rules

1. `true_overloaded`
   - committed_load >= 85
   - execution_load >= 80
   - blocked_ratio < 35

2. `apparent_overloaded`
   - committed_load >= 85
   - execution_load < 60
   - (blocked_ratio >= 35 OR fragmentation >= 60 OR interruption >= 45)

3. `underutilized`
   - committed_load < 50
   - execution_load < 50

4. `balanced`
   - all other cases

## Confidence Score

Confidence increases when more signals are present:

`confidence = min(1.0, 0.35*timesheet_coverage + 0.25*calendar_coverage + 0.25*task_update_freshness + 0.15*dependency_signal_quality)`

## Action Suggestions

- `true_overloaded`: propose reassignment of low-priority tasks.
- `apparent_overloaded`: reduce blockers and context switches first.
- `underutilized`: assign backlog or mentorship tasks.
- `balanced`: maintain current allocation.
