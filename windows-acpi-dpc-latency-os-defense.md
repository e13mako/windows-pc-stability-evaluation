# Modern Standby DPC Latency: What Windows Must Do When Firmware Misbehaves

> **Status:** Draft — Independent research based on real-world ETW trace analysis  
> **Platform:** Windows 11 24H2 / 25H2  
> **Focus:** ACPI.sys / Modern Standby / Firmware interaction

---

## One-Sentence Summary

Repeated 80–100ms ACPI DPC stalls fall below the watchdog threshold but cumulatively degrade UI responsiveness, and Windows currently has no mechanism to mitigate them.

---

## Background

Reproducible 80–100ms DPC latency spikes caused by `ACPIDevicePowerDpc` have been observed across multiple systems during screen-off transitions under Modern Standby.

Root cause analysis ([windows-pc-stability-evaluation](https://github.com/e13mako/windows-pc-stability-evaluation)) confirms that `Sleep()` opcodes executed inside AML power transition paths — at `DISPATCH_LEVEL` — are the direct source of these stalls.

This document addresses a separate but equally important question:

> **Even when OEM firmware misbehaves, what should Windows itself do to protect the user experience?**

---

## The Watchdog Gap

Windows currently provides `DPC_WATCHDOG_VIOLATION` (bugcheck `0x133`) as its primary defense against DPC overruns.
This is not a corner case — it is a reproducible and observable behavior across multiple systems.

| Spike Duration | OS Response |
|---|---|
| Several seconds or more | DPC Watchdog → BSOD |
| **80–140ms, repeated** | **Nothing (silent accumulation → UI collapse)** |
| < 1ms | Normal operation |

The 80ms range falls into a **dead zone**: large enough to degrade user experience cumulatively, small enough to never trigger the watchdog.

This is a structural gap in the Windows power management stack.

---

## What Windows Should Do

### 1. Stop Executing `AML Sleep()` at `DISPATCH_LEVEL`

The current `ACPI.sys` AML interpreter executes `Sleep()` opcodes by busy-waiting at `DISPATCH_LEVEL` — equivalent to `KeStallExecutionProcessor`. This is architecturally incorrect.

**The correct behavior:**

```
AML interpreter encounters Sleep() opcode
  → Suspend current DPC
  → Transfer context to PASSIVE_LEVEL worker thread
  → Wait via timer for specified duration
  → Reschedule DPC on completion
```

With this design, even `Sleep(4ms) × 22` loops in AML would not block `DISPATCH_LEVEL` for 88ms. Other DPCs, ISRs, and UI dispatch would remain unaffected.

---

### 2. AML Method Execution Time Budget

The OS should measure per-AML-method execution time and apply graduated responses when thresholds are exceeded:

| Stage | Action |
|---|---|
| Stage 1 | Emit ETW warning event (debug support) |
| Stage 2 | Throttle power transition frequency for the offending device |
| Stage 3 | Skip D3 transition for that device (maintain function, sacrifice power saving) |

This creates a **graceful degradation** path between "do nothing" and "BSOD" — both of which are currently the only options.

---

### 3. Parallel DPC Scheduling for Modern Standby Transitions

Currently, `ACPIDevicePowerDpc` evaluates `_PS3` for all devices **serially and synchronously** during screen-off transitions.

**Proposed improvement:**

- Queue each device's transition as an **independent DPC**
- Monitor execution time per DPC individually
- Skip timed-out device transitions and proceed to the next

This prevents a single device's AML polling loop from stalling the entire power transition chain.

---

### 4. Cumulative Stall Detection and Auto-Recovery

The UI collapse pattern observed in the field is caused not only by individual DPC spikes, but by the **cascading I/O queue stalls** they trigger — `storport.sys` delays, DPC queue growth — that do not self-recover without a reboot.

**Proposed mechanism:**

> If cumulative `DISPATCH_LEVEL` occupancy exceeds threshold within a measurement window, simplify device power management for the next Modern Standby transition cycle.

This would enable **reboot-free recovery** from the stall accumulation mode.

---

## Microsoft's Recent Movement

As reported by Neowin on April 27, 2026, Microsoft has begun shipping Modern Standby improvements in Windows 11 24H2/25H2:

- Automatic disabling of most wake sources when excessive battery drain is detected
- Input suppression extended to AC-connected systems
- Voice wake excluded as a wake source

This indicates Microsoft is moving in the direction of **OS-side intervention when Modern Standby misbehaves** — a welcome development.

However, these changes address **battery drain**, not **DPC latency accumulation**. The architectural gap described above remains unaddressed.

---

## The Core Argument

> The OS has a responsibility to protect user experience **even when firmware misbehaves**.

The Linux kernel provides partial equivalents: `acpi_enforce_resources`, runtime AML execution monitoring via the AML debugger. Windows `ACPI.sys` has no comparable defense layer.

Given that OEM firmware quality varies dramatically across vendors — as documented across nearly 20 systems in this research — relying solely on "OEMs will write correct AML" is a fragile design assumption.

The 80ms spike that bypasses the watchdog but cumulatively destroys UI responsiveness represents a **blind spot in the Windows power management architecture** that warrants dedicated engineering attention.

This is not merely an optimization problem.

It is a responsibility boundary between firmware and the operating system.

---

## Summary of Proposed Changes

| # | Area | Change |
|---|---|---|
| 1 | `ACPI.sys` AML interpreter | Execute `Sleep()` at `PASSIVE_LEVEL` via worker thread, not busy-wait at `DISPATCH_LEVEL` |
| 2 | AML execution monitoring | Per-method time budget with graduated ETW / throttle / skip response |
| 3 | Modern Standby DPC scheduling | Independent per-device DPC queuing with individual timeout handling |
| 4 | Stall recovery | Cumulative `DISPATCH_LEVEL` occupancy detection with adaptive power management simplification |

---

## Related

- [ACPI DPC Latency Study — Root Cause Analysis](https://github.com/e13mako/windows-pc-stability-evaluation)
- [Analysis: OptiPlex SFF 7020 — 86ms DPC Spike](https://github.com/e13mako/windows-pc-stability-evaluation/blob/main/docs/analysis-dell-7020.md)
---

*Independent research. No vendor attribution intended. Based on ETW trace analysis across multiple production systems.*
