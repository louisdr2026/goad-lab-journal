# 04 — Offline Password Cracking

The captured Kerberos service-ticket hashes were tested with Hashcat using mode `19700`.

The wordlist run completed with:

- 3 total digests
- 1 recovered
- 2 unrecovered

The recovered account was `jon.snow`.

The recovered plaintext credential is intentionally excluded from this repository.

**Result:** Successful partial recovery.
