# 05 — Authenticated SMB Enumeration

The recovered `jon.snow` account was used to perform authenticated SMB enumeration against the GOAD hosts.

The original GOAD documentation contains expected share-access observations that were not reproduced identically in this environment.

The filtered output contained no `READ`/`WRITE` entries.

This does not mean that no shares existed; the underlying raw enumeration remains the source of truth and is stored privately.

**Result:** Authenticated enumeration completed; expected READ/WRITE output not reproduced.
