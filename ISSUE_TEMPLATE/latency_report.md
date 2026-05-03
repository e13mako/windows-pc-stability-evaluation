---
name: DPC Latency Case Report
about: Report ACPI-related latency issues found via LatencyMon or WPA.
title: '[Case] <Vendor> <Model> - <CPU>'
labels: 'case-study'
assignees: ''
---

## 1. System Environment
* **PC Model / Motherboard:** (e.g., Dell OptiPlex 7020, ASUS ROG Z790, etc.)
* **CPU:** (e.g., Intel Core i5-14500, Ryzen 9 7950X)
* **OS Version:** (e.g., Windows 11 24H2, Build 26100.x)
* **BIOS/Firmware Version:** (e.g., v1.4.0)
* **Power Plan:** (e.g., Balanced, High Performance)

## 2. Observed Symptoms
When do you feel the "lag" or "freeze"?
* [ ] Immediately after the screen turns back on (Resume from screen-off)
* [ ] Initial mouse movement after system idle
* [ ] During specific power state transitions
* [ ] Other: (Please describe)

## 3. Evidence (Data)
Please provide at least one of the following:

### A. LatencyMon (Entry Level)
* **Highest DPC routine execution time:** (e.g., 82000 μs / 82 ms)
* **Driver causing the spike:** (Expected: `ACPI.sys`)
* *Attach a screenshot of the "Stats" and "Drivers" tabs if possible.*

### B. WPA (Windows Performance Analyzer) (Intermediate Level)
* **Observed Spike Duration:** (e.g., 80ms - 100ms)
* **Module/Function:** (e.g., `ACPI.sys!ACPIDevicePowerDpc`)
* *Attach a screenshot showing the DPC/ISR spike during the screen-off/resume cycle.*

### C. AML / ACPI Table (Advanced Level)
* If you have extracted the DSDT/SSDT, please paste the suspicious code snippet (e.g., `While` loops with `Sleep`).

## 4. Steps to Reproduce
How can others (or the maintainer) replicate this behavior?
1. (e.g., Set "Screen off" timer to 1 minute)
2. (Wait for the screen to turn off)
3. (Move the mouse and observe the UI freeze)

## 5. Additional Context / Remarks
Any other observations? (e.g., "This issue started after a specific BIOS update," or "The issue disappears when I disable C-States.")
