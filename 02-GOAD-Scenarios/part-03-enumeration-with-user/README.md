# GOAD Part 3 — Enumeration with User

**Status:** `reproduced`  
**Date:** `2026-09-26`  
**GOAD reference:** https://mayfly277.github.io/posts/GOADv2-pwning-part3/  
**Lab:** Authorized GOAD Active Directory lab

## Overview

This document records my reproduction of GOAD Part 3, **Enumeration with User**, using a modern Kali Linux environment and the current GOAD lab.

The original GOAD article is used as the methodological reference. Commands, tool versions, results, deviations, and observations documented here are from my own lab.

Sensitive credentials, Kerberos hashes, raw BloodHound archives, and raw terminal logs are intentionally excluded from this repository.

## Objective

Demonstrate how credentials obtained during Active Directory reconnaissance can be used to perform authenticated enumeration of:

- Active Directory users and LDAP information
- Kerberos service accounts
- SMB shares
- Active Directory-integrated DNS
- BloodHound relationships and trust information

The final objective is to determine whether authenticated enumeration exposes a practical privilege-escalation or attack path.

## Lab Context

Primary domain used during this phase:

`north.sevenkingdoms.local`

Relevant hosts:

| Host | IP | Domain |
|---|---|---|
| WINTERFELL | 192.168.56.11 | north.sevenkingdoms.local |
| CASTELBLACK | 192.168.56.22 | north.sevenkingdoms.local |
| KINGSLANDING | 192.168.56.10 | sevenkingdoms.local |
| MEEREEN | 192.168.56.12 | essos.local |
| BRAAVOS | 192.168.56.23 | essos.local |

## Methodology

The reproduction followed this sequence:

1. User enumeration
2. LDAP enumeration
3. Kerberoasting
4. Offline password cracking
5. Authenticated SMB enumeration
6. AD-integrated DNS enumeration
7. BloodHound collection
8. BloodHound relationship and ACL analysis
9. Attack-path assessment

## Results Summary

| Phase | Result |
|---|---|
| User enumeration | Completed |
| LDAP enumeration | Completed |
| Kerberoasting | Completed |
| Offline cracking | 1 of 3 service-account hashes recovered |
| Authenticated SMB enumeration | Completed; expected READ/WRITE output was not reproduced |
| AD DNS enumeration | Zone discovery successful; record query returned zero records |
| BloodHound collection | Successful |
| BloodHound analysis | Completed |
| ACL-based privilege escalation | Not demonstrated |

## Credential Recovery

Kerberoasting produced three service-ticket hashes for offline analysis.

Hashcat completed the supplied wordlist with:

- 3 total digests
- 1 recovered
- 2 unrecovered

The recovered service account was `jon.snow`.

The plaintext password is deliberately not documented here.

The recovered credential was subsequently verified against SMB authentication in the authorized lab.

## Authenticated Enumeration

The recovered account enabled authenticated enumeration against the GOAD environment.

SMB enumeration did not reproduce the exact READ/WRITE result shown in the original GOAD documentation. The raw output is retained privately as evidence.

This is documented as a lab/version/build difference rather than treating the discrepancy as a failure.

## AD DNS Enumeration

Authenticated `adidnsdump` enumeration successfully discovered DNS zones including:

- `north.sevenkingdoms.local`
- `RootDNSServers`
- `_msdcs.sevenkingdoms.local`
- `essos.local`

The subsequent forest record query returned zero records.

Therefore:

> DNS zone discovery succeeded, while the queried record enumeration returned no records.

The repository copy of `records.csv` contains only its header and contains no useful internal DNS records.

## BloodHound

BloodHound collection was successfully performed using the recovered account.

The collection identified:

- 17 users
- 51 groups
- 2 computers
- 2 forest domains
- 1 trust

The collector reported Global Catalog warnings. Collection nevertheless completed successfully.

### Jon Snow relationships

`JON.SNOW` was identified as a member of:

- `NIGHT WATCH`
- `STARK`

The account was not directly identified as a member of `DOMAIN ADMINS`.

### Privileged relationships

`DOMAIN ADMINS` contained:

- `EDDARD.STARK`
- built-in `ADMINISTRATOR`

The domain `ADMINISTRATORS` group contained several privileged principals, including `DOMAIN ADMINS`.

Importantly, `STARK` was not observed as nested into `DOMAIN ADMINS`.

### ACL analysis

A complete inspection of the collected user and group objects returned:

- 0 user ACEs
- 0 group ACEs

The examined users also showed no:

- `AllowedToDelegate`
- `SPNTargets`
- `SIDHistory`

Accordingly, this BloodHound collection did **not demonstrate an ACL-based privilege-escalation path from `JON.SNOW`**.

## Attack Chain Demonstrated

The practical chain reproduced during Part 3 was:

```text
AD reconnaissance
      |
      v
Kerberoasting
      |
      v
Offline password cracking
      |
      v
jon.snow credentials recovered
      |
      v
Authenticated SMB / LDAP / DNS enumeration
      |
      v
BloodHound relationship mapping
      |
      v
Attack-path assessment
