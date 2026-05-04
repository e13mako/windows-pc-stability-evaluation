# Mitigation Strategies (Temporary Workarounds)

## Overview

This document describes temporary mitigation strategies for reducing DPC latency spikes caused by ACPI firmware behavior.

These are not root-cause fixes.
They aim to reduce the frequency or impact of latency events.

---

## 1. Disable Modern Standby (CsEnabled)

### Description

Disabling Modern Standby forces the system to use legacy S3 sleep instead of S0ix.

### Method

Modify registry:

```
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Power
CsEnabled = 0
```

### Effect

* Reduces ACPI power transition frequency
* Avoids problematic S0ix execution paths

### Trade-offs

* Loss of instant-on behavior
* Increased resume time
* Not supported on all systems

---

## 2. Adjust Screen-Off Behavior

### Description

Avoid frequent screen-off transitions that trigger ACPI power routines.

### Recommendations

* Increase screen-off timeout
* Avoid aggressive power-saving settings

### Effect

* Reduces number of ACPI transition events
* Lowers probability of latency spikes

---

## 3. Disable Unused Devices

### Description

Disable components that trigger ACPI power transitions.

### Examples

* Audio devices (e.g., onboard Realtek audio)
* Wireless adapters (if not needed)

### Effect

* Reduces number of `Notify()` events
* Simplifies ACPI execution path

---

## 4. Replace Problematic Hardware

### Description

Some devices correlate strongly with latency behavior.

### Examples

* Replace Realtek Wi-Fi with Intel-based adapters

### Effect

* Improves driver and firmware interaction
* Reduces latency variability

---

## 5. Operational Workarounds

### Description

Adjust usage patterns to avoid problematic states.

### Examples

* Restart systems periodically
* Avoid long uptime with repeated sleep cycles
* Shut down devices when not in use

---

## Important Note

These strategies do not fix the underlying issue.

The root cause remains firmware-level behavior in ACPI implementation.

---

## Key Insight

Mitigation reduces exposure to problematic execution paths.

It does not eliminate them.
