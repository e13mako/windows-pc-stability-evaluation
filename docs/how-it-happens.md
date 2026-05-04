## How ACPI Causes DPC Latency

## Overview

This document explains how ACPI firmware behavior can lead to observable DPC latency spikes.

---

## Execution Flow

1. System enters low-power or screen-off state
2. User interaction triggers resume
3. ACPI power transition begins
4. AML methods execute (DSDT/SSDT)
5. Blocking loop occurs:

```asl
   While (Condition)
   {
       Sleep(4)
   }
```

6. ACPI.sys waits for completion
7. DPC queue is delayed
8. System input appears frozen

---

## Key Insight

The delay is not caused by CPU performance or OS scheduling.

It is introduced by firmware-level waiting logic inside ACPI execution paths.
