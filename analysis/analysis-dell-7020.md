# Analysis Report: ACPI DPC Latency and DellRtd3 Implementation

## 1. Overview

This report analyzes DPC (Deferred Procedure Call) latency spikes observed during screen-off and idle transitions on a desktop platform.

The investigation focuses on firmware behavior rather than hardware capability, identifying ACPI implementation patterns that directly impact system responsiveness.

---

## 2. System Configuration

* Model: Dell OptiPlex SFF 7020
* CPU: Intel Core i5-14500 (Raptor Lake Refresh)
* BIOS Version: 1.24.0 (2026-01-27)
* OS Version: Windows 11 24H2 (Build 26100.4349)
* Storage: NVMe via Intel VMD Controller (DEV 467F) – iaStorVD.sys

---

## 3. Observed Symptoms

During screen-off transitions (Video Idle Timeout), the following latency events occur reproducibly:

* ACPI.sys (`ACPIDevicePowerDpc`): up to 86.3 ms
* Storport.sys: up to 79.26 ms

These spikes result in noticeable UI unresponsiveness when the system resumes from idle.

---

## 4. Execution Flow (Simplified)

1. Screen turns off (idle timeout)
2. ACPI triggers power transition
3. `\_SB.PEPD._DSM` method is executed
4. Multiple `Notify()` events are issued
5. `POFF()` method is invoked (SSDT4 / DellRtd3)
6. `Sleep()` is executed inside ACPI interpreter
7. `ACPIDevicePowerDpc` is blocked (~80ms)
8. UI becomes temporarily unresponsive

---

## 5. Technical Analysis and Root Cause

Trace data indicates that latency spikes are triggered during execution of the `\_SB.PEPD._DSM` method.

Key observations:

* Multiple device notifications occur in cascade during power transitions
* `ACPIDevicePowerDpc` duration (86.3 ms) aligns with a specific `Notify()` event
* `POFF()` methods in SSDT4 (DellRtd3) execute `Sleep()` inside ACPI execution context
* This introduces blocking behavior during DPC execution

Additional findings:

* DSDT `HPEM` method contains a busy-wait loop with up to 1000 ms delay potential
* Identical code exists in both `If` and `Else` branches (likely incomplete refactoring)

---

## 6. Key Finding

The primary cause of the latency spike is the execution of blocking `Sleep()` calls inside ACPI power transition paths, specifically within the `POFF()` method of DellRtd3.

This occurs within the ACPI execution path associated with DPC handling, directly delaying system responsiveness.

---

## 7. Comparative Analysis

Cross-system observations suggest:

* Some implementations include conditional checks that avoid unnecessary power transitions
* The tested OptiPlex 7020 executes `POFF()` more aggressively
* Device/port configuration influences how often the routine is triggered

These differences explain variability in latency behavior across systems with similar hardware.

---

## 8. Why This Matters

* The issue is not visible in standard benchmarks
* It does not generate system errors or warnings
* It directly affects perceived responsiveness

As a result, the problem is difficult to detect but has real-world impact on user experience.

---

## 9. Recommendations

### 1. Avoid blocking operations in ACPI execution paths

* Remove or redesign `Sleep()` usage in AML methods such as `POFF()`

### 2. Replace polling loops with event-driven logic

* Eliminate busy-wait loops in methods like `HPEM`

### 3. Correct conditional logic

* Fix redundant or improperly structured branches to ensure efficient execution paths

---

## 10. Conclusion

The observed latency is not a limitation of CPU performance or OS scheduling.

It is introduced by firmware-level design decisions in ACPI implementation.

Understanding these patterns allows system behavior to be predicted, evaluated, and improved.
