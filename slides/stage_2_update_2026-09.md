---
marp: true
theme: default
paginate: true
footer: 'Intl Sequence Units - Stage 2 Update'
style: |
  .columns {
    display: grid;
    grid-template-columns: repeat(2, minmax(0, 1fr));
    gap: 1rem;
  }
---

<!-- 
_class: lead 
_backgroundColor: #2c3e50
_color: #ffffff
-->

# Intl Sequence Units
## Stage 2 Update
### September 2026 TC39

---

## Background: What are Sequence Units?

Measurement systems frequently employ multiple units in sequence to express a single magnitude.

**Example sanctioned units:**

- Length: `foot-and-inch`, `meter-and-centimeter`
- Mass: `pound-and-ounce`, `kilogram-and-gram`

---

## Issue #16: Array as input

TG2 approved switching from an object to an array:
- **Reduces footguns**: Prevents mismatched or extra keys not in the unit ID
- **Easier invariants**: Non-smallest parts must be integers; array length matches sequence

<div class="columns">
<div>

**Old (Object):**
```javascript
const nf = new Intl.NumberFormat('en-US', {
  style: 'unit',
  unit: 'meter-and-centimeter',
});

nf.format({ meter: 1, centimeter: 80 });
// "1 m, 80 cm"
```

</div>
<div>

**New (Array):**
```javascript
const nf = new Intl.NumberFormat('en-US', {
  style: 'unit',
  unit: 'meter-and-centimeter',
});

nf.format([1, 80]);
// "1 m, 80 cm"
```

</div>
</div>

---

## Issue #14: Significant digit options

- **The Problem:** Sequence units use non-decimal ratios (12 in/ft, 16 oz/lb). Significant digits across mixed units are ill-defined:
  - Applying sigfigs to the overall magnitude: 5 ft 10 in has 2 significant digits, but it is silly to round 5 ft 6 in to 5 ft 10 in
  - Applying sigfigs to the least significant unit: 2 sigfigs could produce `"3 ft, 0.18 in"`, which is not 2 significant digits overall
- **TG2 Recommendation:** Disallow significant digit options for sequence units; throw a `RangeError` if configured (tracked by internal `[[RoundingType]]`).
- **Note:** Fraction digits (`minimumFractionDigits`, `maximumFractionDigits`) remain supported, applied to the terminal sub-unit only.

---

## Issue #8: Time/duration units

- **July Plenary Discussion:**
  - *Exclude from NumberFormat:* Duration units have domain-specific complexities (DST boundaries, calendar month lengths) and already have dedicated APIs (`Temporal.Duration`, `Intl.DurationFormat`).
  - *Include for Amount:* CLDR defines duration units/conversions; developers expect unified handling.
- **Suggested Path Forward:**
  - `Intl.NumberFormat` keeps its current list of sanctioned units, without time units
  - `Amount` supports representing time units and limited conversions from CLDR
  - `Amount.prototype.toLocaleString` delegates to `Intl.DurationFormat` for time units

---

## Summary & Discussion

- **Issue #16:** Array input (`[1, 80]`) — reduces footguns, easier invariants
- **Issue #14:** Disallow significant digits — avoids non-decimal ambiguity
- **Issue #8:** Exclude time units from `Intl.NumberFormat` — delegate via `Amount`
- **Stage 2.7 Reviewers:** [Eemeli Aro (EAO) and Dan Minor (DLM)](https://github.com/tc39/notes/blob/main/meetings/2026-05/may-20.md#intl-sequence-units-for-stage-1-or-2)
