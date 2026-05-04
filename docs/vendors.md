# Vendor Tendencies (Observed)

## Overview

Different vendors exhibit different ACPI implementation styles.

These tendencies are observational and not absolute.

---

## EPSON

- Minimal external dependency
- Simple AML structure
- Very low DPC latency

---

## HP

- Balanced implementation
- Limited custom extensions
- Stable in most cases

---

## Dell

- Complex ACPI structure
- External dependency (EC / device response)
- Potential for large latency spikes

---

## Lenovo (Think series)

- Generally stable
- Moderate complexity

---

## Common Risk Factors

- Realtek Wi-Fi
- Heavy ACPI abstraction
- Long wait loops

---

## Important Note

These are tendencies, not guarantees.

Individual models may differ.
