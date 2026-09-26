# 07 — BloodHound

Authenticated BloodHound collection was performed with `bloodhound-python`.

The collection identified:

- 17 users
- 51 groups
- 2 computers
- 2 forest domains
- 1 trust

Global Catalog warnings were reported because the collection did not resolve through a Global Catalog, but collection completed successfully.

## Jon Snow

`JON.SNOW` was identified as a member of:

- `NIGHT WATCH`
- `STARK`

No direct membership in `DOMAIN ADMINS` was identified.

## ACL Analysis

Inspection of the collected user and group objects found:

- 0 user ACEs
- 0 group ACEs

No relevant `AllowedToDelegate`, `SPNTargets`, or `SIDHistory` relationships were identified on the examined users.

Therefore, the collection did not demonstrate an ACL-based privilege-escalation path from `JON.SNOW`.

**Result:** BloodHound collection and analysis completed.
