# FAQ

## Is this a CPU performance issue?

No.

Systems with identical CPUs can behave very differently.

---

## Is this a Windows problem?

Partially.

Windows executes ACPI, but the behavior depends on firmware.

---

## Can this be fixed by drivers?

Sometimes.

But if the issue originates in AML, it is firmware-dependent.

---

## Why is this not widely discussed?

- Hard to observe without tools
- Does not always cause crashes
- Appears as “random sluggishness”

---

## Does Modern Standby make this worse?

Yes.

More frequent power transitions increase exposure to problematic ACPI paths.
