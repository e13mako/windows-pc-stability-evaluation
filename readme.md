# ACPI DPC Latency Study

### Why your system freezes for 80ms — and nobody notices

![Status](https://img.shields.io/badge/status-ongoing-blue)
![Platform](https://img.shields.io/badge/platform-Windows%2011-informational)
![Focus](https://img.shields.io/badge/focus-ACPI%20%2F%20Firmware-critical)

---

## The Problem

Modern systems promise instant responsiveness after waking from sleep.  
In practice, many deliver 80–100ms freezes instead — silently, repeatedly, by design.

This repository documents an independent investigation into a class of DPC latency spikes triggered during ACPI power transitions, with a focus on the firmware patterns that cause them.

---

## Key Finding

> **The same CPU generation can exhibit 10×–100× different latency behavior depending solely on firmware implementation.**

Across multiple test systems sharing identical hardware generations and OS versions (Windows 11 24H2 / 25H2), latency behavior differed dramatically — pointing to firmware, not silicon, as the root variable.

---

## The Core Pattern

A recurring AML construct found in ACPI power transition paths:

```asl
While (Condition)
{
    Sleep(4)   // repeated hundreds of times
}
```

This loop runs inside ACPI's critical timing window — blocking execution and producing observable system lag, input freeze, and in severe cases, forcing a hard power-off.

**This is not a hardware defect. It is a firmware design outcome.**

---

## Scope of Investigation

| Area                    | Description                                |
| ----------------------- | ------------------------------------------ |
| `ACPI.sys` behavior     | `ACPIDevicePowerDpc` execution profiling   |
| AML analysis            | DSDT/SSDT extraction and structural review |
| WPR/WPA tracing         | Trace correlation to firmware events       |
| Cross-system comparison | Multiple OEMs, Intel and AMD platforms     |
| Firmware lineage        | Pattern classification across vendors      |

---

## A Broader Pattern

Similar AML loop structures appear across multiple vendors — with near-identical control flow and implementation style. This suggests a shared design lineage or common reference origin rather than independent convergence.

The investigation treats this as a structural observation, not a vendor-specific accusation.

---

## Real-World Impact

On affected systems:

1. Screen turns off (idle timeout or manual)
2. User returns and attempts interaction
3. System is unresponsive for 80–100ms or more
4. In severe cases: complete UI failure, forced power-off required

This pattern is reproducible under controlled conditions and correlates directly with ACPI power transition events in trace data.

---

## Methodology

- Clean OS environments (no third-party drivers where avoidable)
- Scripted screen-off / wake cycles for reproducibility
- WPR/WPA trace capture and event correlation
- AML extraction via standard tools (e.g., `acpidump`, `iasl`)
- Structural comparison across multiple firmware images

---

## Repository Structure

```
docs/        Findings, analysis write-ups, and annotated traces
aml/         Extracted and sanitized AML structures
analysis/    Pattern classification and cross-system comparison
data/        Supporting trace data and measurement results
```

---

## Limitations and Disclosure

Some implementation details are intentionally abstracted to avoid exposing vendor-specific internals prematurely.  
This work is descriptive and structural — not a CVE, not a blame assignment.

---

## Status

- [x] Initial latency characterization
- [x] AML pattern identification
- [ ] Cross-platform pattern classification (Intel / AMD)
- [ ] Firmware lineage analysis
- [ ] Public findings document

---

## If You've Seen This

If you've observed similar latency behavior — especially post-idle freezes on Windows 11 with Modern Standby — open a Discussion with your trace data or system details.

Corroborating observations from other hardware configurations are valuable.

---

> _Latency is not just a hardware property — it is a firmware implementation outcome._

_Independent research. Based on real-world systems and trace analysis._
