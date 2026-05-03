# ACPI DPC Latency Study

### Why your system freezes for 80ms — and nobody notices

![Status](https://img.shields.io/badge/status-ongoing-blue)
![Platform](https://img.shields.io/badge/platform-Windows%2011-informational)
![Focus](https://img.shields.io/badge/focus-ACPI%20%2F%20Firmware-critical)

---

## Who This Is For

Engineers, system integrators, and IT professionals who need to understand why identical Windows systems behave differently under real-world conditions.

---

## The Problem

Modern systems promise instant responsiveness after waking from sleep.  
In practice, many deliver 80–100ms freezes instead — silently, repeatedly, by design.

These delays are typically invisible to standard benchmarks and rarely trigger system errors, yet they directly impact user interaction and perceived performance.

---

## Key Finding

> **The same CPU generation can exhibit 10×–100× different latency behavior depending solely on firmware implementation — not hardware.**

Across multiple systems with identical CPU generations and OS builds (Windows 11 24H2 / 25H2), latency behavior varied dramatically.

This points to firmware — specifically ACPI implementation — as the dominant variable.

---

## Reproducible Symptom

On affected systems:

1. Screen turns off (idle timeout or manual)
2. User returns and attempts interaction
3. System becomes unresponsive for 80–100ms or longer
4. In severe cases, UI fails to recover and requires a hard power-off

This behavior is reproducible and correlates directly with ACPI power transition activity.

---

## The Core Pattern

A recurring AML construct observed in multiple firmware implementations:

```asl
While (Condition)
{
    Sleep(4)
}

When executed inside ACPI power transition paths, this loop introduces blocking delays that manifest as input lag and UI freezes.

This is not a hardware limitation.
It is a firmware implementation decision.

Evidence
WPA traces showing DPC spikes during ACPIDevicePowerDpc
Correlation between AML execution paths and latency events
Cross-system comparison with identical hardware but different firmware
Consistent reproduction under controlled test conditions
Methodology
Clean OS environments (minimal third-party interference)
Controlled screen-off / wake cycles
WPR/WPA trace capture and timing analysis
AML extraction using standard tooling (acpidump, iasl)
Structural comparison across firmware implementations
Scope of Investigation
Area	Description
ACPI.sys behavior	ACPIDevicePowerDpc profiling
AML analysis	DSDT/SSDT extraction and review
WPR/WPA tracing	Event-level correlation
Cross-system comparison	Multi-vendor, Intel and AMD platforms
Firmware lineage	Pattern classification and similarities
A Broader Pattern

Similar AML loop structures appear across multiple vendors with near-identical control flow.

This suggests a shared design lineage or common reference implementation rather than independent design choices.

This work focuses on structural behavior, not vendor attribution.

Repository Structure
docs/        Findings and analysis
aml/         Extracted AML structures
analysis/    Pattern classification
data/        Trace data and measurements
templates/   WPA evaluation templates
Limitations

Some implementation details are intentionally abstracted to avoid exposing vendor-specific internals prematurely.

This is a structural and behavioral study, not a vulnerability disclosure.

Status
Initial latency characterization: complete
AML pattern identification: complete
Cross-platform classification: in progress
Firmware lineage analysis: in progress
Public findings document: planned
Contributing / Data Sharing

If you have observed similar behavior:

Post-idle freezes on Windows 11
Input lag after screen-off
Unexplained DPC spikes

You can contribute by opening a Discussion with:

System model and configuration
WPA trace data (if available)
Reproduction steps

Cross-system data significantly improves pattern validation.

Conclusion

Latency is not determined solely by hardware capability.

It emerges from the interaction between firmware, OS, and power management design.

Understanding that structure makes the behavior predictable.

Statement

Latency is not a hardware limitation — it is a firmware decision.

Independent research based on real-world systems and trace analysis.
```
