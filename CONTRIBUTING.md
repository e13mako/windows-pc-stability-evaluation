# Contributing to ACPI DPC Latency Study

Thank you for your interest in this research. 

This project investigates why identical Windows systems exhibit dramatically different latency behavior depending on firmware implementation rather than hardware capability. Our goal is to collect real-world data and identify common AML patterns causing these 80–100ms UI freezes.

---

## How You Can Contribute

We are actively collecting trace data and case reports from users experiencing:
* Post-idle or resume-from-standby freezes.
* Input lag after the screen turns off.
* Unexplained DPC spikes related to `ACPI.sys`.

### What Data We Are Looking For
To help us map firmware implementations across different vendors, please provide the following information:

1. **System Environment**: 
   - PC model / Motherboard and CPU generation
   - BIOS/Firmware version
   - OS version (e.g., Windows 11 24H2)
2. **LatencyMon Data**: 
   - Highest DPC routine execution time (μs) and the driver causing the spike.
3. **WPA Trace Data** (Optional): 
   - Screenshots or exported data showing `ACPIDevicePowerDpc` spikes during the screen-off/resume cycle.
4. **AML Code** (Optional): 
   - Suspicious `Sleep` loop structures extracted from DSDT/SSDT tables, if analyzed.

### How to Submit
* **Case Reports**: Please use the **DPC Latency Case Report** template from the Issues tab.
* **Discussions**: If you have questions, workarounds, or comparisons with different firmware revisions, please open a discussion.

---

*Statement: Latency is not a hardware limitation — it is a firmware decision.*
