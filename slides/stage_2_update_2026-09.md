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

- **The Problem:** Significant digits across mixed units are ill-defined:
  - _Applying sigfigs to the overall digits:_ Could round 5 ft 11 in to 5 ft 10 in
  - _Applying sigfigs to the least significant unit:_ Could produce 3 ft 0.18 in
- **TG2 Recommendation:** Disallow significant digit options for sequence units; throw a `RangeError` if configured (tracked by internal `[[RoundingType]]`).
- **Note:** Fraction digits (`minimumFractionDigits`, `maximumFractionDigits`) remain supported, applied to the terminal sub-unit only.

---

## Issue #8: Time/duration units

July Plenary Discussion:

- **Intl perspective:**
    - Durations have domain quirks (DST, calendar month lengths)
    - `Intl.DurationFormat` & `Temporal.Duration` are purpose-built
- **Amount perspective:**
    - `Amount` is a unified value type for all units
    - CLDR `units.xml` defines time units and conversion factors

---

## Issue #8: Suggested Path Forward

- **Intl.NumberFormat:** Continue to exclude time units from sequence units
- **Amount:** Supports time units to the scope in CLDR units.xml; delegate formatting to `Intl.DurationFormat` (details to be discussed and brought back in an update to Amount)

```javascript
// Intl.NumberFormat rejects time sequence units:
new Intl.NumberFormat('en-US', { style: 'unit', unit: 'hour-and-minute' });
// ❌ RangeError

// Amount supports time units, delegating formatting:
const amt = new Amount([2, 30], 'hour-and-minute');
amt.toLocaleString('en-US');
// "2 hr, 30 min" (via Intl.DurationFormat)
```

---

## Summary & Discussion

- **Issue #16:** Array input (`[1, 80]`) — reduces footguns, easier invariants
- **Issue #14:** Disallow significant digits — avoids non-decimal ambiguity
- **Issue #8:** Continue to exclude time sequence units from `Intl.NumberFormat`

Next time: approaching Stage 2.7. (Reviewers are [Eemeli Aro (EAO) and Dan Minor (DLM)](https://github.com/tc39/notes/blob/main/meetings/2026-05/may-20.md#intl-sequence-units-for-stage-1-or-2))
