# How to Evaluate a System

## Goal

Determine whether a system is likely to exhibit DPC latency issues caused by firmware.

---

## Step 1: Capture WPA Trace

- Use WPR with CPU + DPC/ISR profile
- Record:
  - Idle
  - Interaction
  - Screen-off → resume

---

## Step 2: Check DPC/ISR Usage

Look for:

- Spikes >50ms → suspicious
- Spikes >100ms → problematic

---

## Step 3: Identify Module

Focus on:

- `ACPI.sys`
- Network drivers (e.g., Wi-Fi)

---

## Step 4: Correlate Trigger

Common triggers:

- Screen-off / resume
- Device power transition
- Network reconnect

---

## Step 5: Inspect AML (Optional but powerful)

Extract DSDT/SSDT:

- Look for loop patterns
- Look for Sleep() inside loops

---

## Evaluation Criteria

| Rating | Description |
|--------|------------|
| A | No significant spikes |
| B | Minor spikes (<50ms) |
| C | Noticeable spikes (50–100ms) |
| D | Severe spikes (>100ms) |

---

## Key Insight

You are not measuring performance.

You are detecting structural instability.
