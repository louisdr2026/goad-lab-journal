# 06 — AD-Integrated DNS Enumeration

`adidnsdump` 1.4.0 was installed in an isolated Python virtual environment.

Authenticated enumeration successfully discovered:

- `north.sevenkingdoms.local`
- `RootDNSServers`
- `_msdcs.sevenkingdoms.local`
- `essos.local`

The subsequent forest record query returned zero records.

The repository copy of `records.csv` contains only the CSV header.

**Result:** Zone discovery successful; record enumeration returned zero records.
