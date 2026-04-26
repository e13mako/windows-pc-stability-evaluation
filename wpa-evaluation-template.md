# WPA Evaluation Template

## Overview
This template is used to evaluate Windows PC stability based on DPC latency and system behavior.

---

## Test Conditions

- Duration: 5–10 minutes
- State:
  - Idle (1 min)
  - Light interaction (Explorer, right-click)
  - Network active (Wi-Fi connected)
  - Resume from sleep (if applicable)
- Power: AC or Battery (must be consistent)
- Background apps: Default state

---

## System Information

- Model:
- CPU:
- BIOS/UEFI:
- Wi-Fi:
- OS Build:

---

## DPC Latency

### Max DPC Latency
- Value: ____ ms

Evaluation:
- [ ] <100 ms (Excellent)
- [ ] 100–150 ms (Acceptable)
- [ ] 150–200 ms (Warning)
- [ ] >200 ms (Critical)

---

### Spike Frequency
- Count: ____ / 5 min

Evaluation:
- [ ] None or rare
- [ ] Occasional
- [ ] Frequent

---

## ISR (Interrupt Service Routine)

- Max ISR: ____ ms
- Notes:

---

## Top Drivers (by DPC/ISR)

1. __________________
2. __________________
3. __________________

Category:
- [ ] Network (Wi-Fi)
- [ ] Storage
- [ ] GPU
- [ ] ACPI
- [ ] Other

---

## Behavior Analysis

- Occurs under load: Yes / No
- Occurs when idle: Yes / No

Trigger:
- [ ] User interaction
- [ ] Network activity
- [ ] Resume from sleep
- [ ] Random

---

## Overall Rating

- [ ] A (Stable)
- [ ] B (Minor issues)
- [ ] C (Needs mitigation)
- [ ] D (Not recommended)

---

## Notes

(Free text)
