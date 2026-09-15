# Module 1.1 — Predicate Specification

Specification of the hard rules for the Invigilator Assignment Problem (IAP) using first-order logic (FOL).

## Predicates used

- `Assign(i,j)` — invigilator `i` is assigned to shift `j`.
- `Busy(i,j)` — invigilator `i` is busy at shift `j`.
- `Overlap(j,k)` — shifts `j` and `k` overlap in time; this predicate is symmetric and irreflexive.
- `AtCampus(j,c)` — shift `j` takes place at campus `c`.
- `Role(i,j,r)` — invigilator `i` holds role `r` at shift `j`.
- `Date(j)`, `StartTime(j)`, `EndTime(j)` — the date, start time, and end time of shift `j`.

Parameters (not predicates — they do not evaluate to true/false):
- `roleCapacity(j,r)` = number of people required for role `r` at shift `j`.
- `MinBuffer` = minimum time (in minutes) required to travel between the two campuses plus arrive early, chosen by the team and justified in `DECISIONS.md`.


## Rule 1 - No double-booking

An invigilator cannot be assigned to two shifts that overlap in time.

```
∀i ∀j ∀k ( Overlap(j,k) ∧ Assign(i,j) → ¬Assign(i,k) )
```

```
∀j ∀k ( Overlap(j,k) → Overlap(k,j) ) (symmetric)
∀j ¬Overlap(j,j) (irreflexive)
```

## Rule 2 - Availability

A busy invigilator cannot be assigned to that shift.

```
∀i ∀j ( Busy(i,j) → ¬Assign(i,j) )
```

## Rule 3 - Capacity (Extension for 1.3: Counting rule)

The total number of people assigned to a shift, for a given role, must exactly match the required count.

```
∀j ∀r ( Σᵢ [Assign(i,j) ∧ Role(i,j,r)] = roleCapacity(j,r) )
```

The total capacity of a shift is a derived quantity, not a separate rule:

```
capacity(j) := Σᵣ roleCapacity(j,r)
```

## Rule 4 - Role uniqueness

An invigilator holds exactly one role within a given shift.

```
∀i ∀j ( Assign(i,j) → ∃!r Role(i,j,r) )
```

## Rule 5 - Cross-campus travel-time

An invigilator cannot cover two time-adjacent shifts at two different campuses if the time gap between them is smaller than the minimum required travel time.

Define the time gap between two same-day shifts (independent of which one comes first):

```
Gap(j,k) := max( 0, StartTime(j) − EndTime(k), StartTime(k) − EndTime(j) )
```

```
∀i ∀j ∀k ∀c1 ∀c2 (
    j≠k ∧ Date(j)=Date(k) ∧ Gap(j,k) < MinBuffer
    ∧ AtCampus(j,c1) ∧ AtCampus(k,c2) ∧ c1≠c2
    ∧ Assign(i,j) → ¬Assign(i,k)
)
```

## Rule 6 - Eligibility — considering


