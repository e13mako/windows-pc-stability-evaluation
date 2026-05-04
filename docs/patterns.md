# AML Patterns That Cause DPC Latency

## Overview

This document summarizes common AML (ACPI Machine Language) patterns observed in firmware that correlate with DPC latency spikes.

The focus is on structural behavior rather than vendor-specific implementation.

---

## 1. Blocking Loop with Sleep()

### Pattern

```asl
While (Condition)
{
    Sleep(4)
}
```

### Why it is problematic

* Executes inside ACPI power transition paths
* Blocks execution repeatedly
* Delay accumulates over loop iterations
* Often runs in timing-sensitive contexts

### Impact

* DPC latency spikes (50–100ms or more)
* Input lag during resume or device transitions
* UI unresponsiveness

---

## 2. Busy-Wait Loop (Polling)

### Pattern

```asl
While (Condition)
{
    // No sleep or very small delay
}
```

### Why it is problematic

* Continuously consumes execution time
* Exit condition depends on external state (EC, device response)
* Can run for extended periods under certain conditions

### Impact

* CPU stalls within ACPI execution
* Potential long blocking periods (100ms–1000ms)
* Severe responsiveness degradation

---

## 3. Nested Loop Structures

### Pattern

```asl
While (Condition1)
{
    While (Condition2)
    {
        Sleep(1)
    }
}
```

### Why it is problematic

* Multiplies delay effects
* Hard to predict total execution time
* Often dependent on multiple external states

### Impact

* Latency spikes become inconsistent and harder to diagnose
* Increased worst-case delay

---

## 4. External Dependency Wait (EC / Device Polling)

### Pattern

```asl
While (EC_FLAG == 0)
{
    Sleep(5)
}
```

### Why it is problematic

* Depends on hardware response timing
* Firmware waits synchronously for external completion
* No guarantee of quick exit

### Impact

* Latency varies based on device state
* Unstable and environment-dependent behavior

---

## 5. Excessive Notify() Cascades

### Pattern

* Multiple `Notify()` calls triggered in sequence during power transitions

### Why it is problematic

* Triggers repeated ACPI method execution
* Increases total execution time indirectly
* Amplifies impact of other problematic patterns

### Impact

* Burst-like DPC latency spikes
* Correlation with device power state changes

---

## 6. Redundant or Inefficient Branching

### Pattern

```asl
If (Condition)
{
    // Code A
}
Else
{
    // Code A (same as above)
}
```

### Why it is problematic

* Indicates incomplete refactoring
* Adds unnecessary execution overhead
* Makes behavior harder to reason about

### Impact

* Minor performance overhead
* Increased complexity and maintenance risk

---

## Not All Loops Are Problematic

Short, bounded loops with fast exit conditions are generally safe.

### Acceptable characteristics

* Small iteration count
* Deterministic exit condition
* Minimal or no Sleep() usage
* Not executed in critical timing paths

---

## Key Insight

Latency issues are not caused by the presence of loops alone.

They arise when:

* Loop duration is unbounded or externally dependent
* Blocking operations (e.g., Sleep) are used in critical paths
* Execution occurs during ACPI power transitions

---

## Practical Takeaway

When reviewing AML:

* Look for loops first
* Check for Sleep() inside loops
* Identify external dependencies (EC, device state)
* Consider execution context (power transition vs idle path)

These patterns are strong indicators of potential DPC latency issues.
