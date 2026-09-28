---
marp: true
theme: default
paginate: true
footer: 'Intl Sequence Units - Stage 2 Update'
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

Old:

```javascript
const nf = new Intl.NumberFormat('en-US', {
  style: 'unit',
  unit: 'meter-and-centimeter',
  unitDisplay: 'long',
});

nf.format({ meter: 1, centimeter: 80 });
// "1 meter, 80 centimeters"
```

New:

```javascript
const nf = new Intl.NumberFormat('en-US', {
  style: 'unit',
  unit: 'meter-and-centimeter',
  unitDisplay: 'long',
});

nf.format([1, 80]);
// "1 meter, 80 centimeters"
```

---

## Issue #14: Significant digit options

- **Topic:** Handling significant digit options (`maximumSignificantDigits`, `minimumSignificantDigits`, and rounding priorities) when formatting sequence units.
- **Background:** Sequence units frequently use non-decimal ratios (e.g. 12 inches/foot, 16 oz/lb). Significant digit rounding across compound fields is ill-defined and brittle (e.g. how to distribute significant digits or round across non-decimal boundaries).
- **TG2 Recommendation:** Throw an exception whenever `Intl.NumberFormat` is configured with significant digit rounding (as tracked by the internal `[[RoundingType]]` slot).

---

## Issue #8: Time/duration units

- Discussed at length at the previous plenary
- Suggested path forward:
    - `Intl.NumberFormat` keeps its current list of sanctioned units, without time units
    - `Amount` supports the representation of time units and limited conversions defined by CLDR units.xml
    - `Amount.prototype.toLocaleString` delegates to `Intl.DurationFormat` for time units

---

<!-- 
_class: lead 
_backgroundColor: #2c3e50
_color: #ffffff
-->

# Discussion
