# GOAD Scenario: Part 01 — Reconnaissance and Scan

**Status:** reproduced  
**Date:** 2026-08-27  
**GOAD reference:** https://mayfly277.github.io/posts/GOADv2-pwning_part1/  
**Authorization/scope:** Authorized, isolated VMware GOAD lab on `192.168.56.0/24`.

## Attribution

This is my reproduction of Mayfly’s GOAD Part 1 methodology. The reference provides the scenario context; commands, results, and observations below are from my own lab.

## Objective

Identify live GOAD hosts, map domains and domain controllers, and enumerate exposed TCP services.

## Execution summary

| Step | Action | Result |
| --- | --- | --- |
| 1 | Attached Kali to VMware host-only network `VMnet1` | Kali received `192.168.56.133/24` on `eth1`. |
| 2 | Ran an unauthenticated SMB discovery sweep with NetExec | Identified five Windows hosts across three Active Directory domains. |
| 3 | Queried LDAP DC SRV records | Confirmed `KINGSLANDING`, `WINTERFELL`, and `MEEREEN` as domain controllers. |
| 4 | Added verified host-to-IP mappings to `/etc/hosts` | FQDN resolution worked during subsequent scanning. |
| 5 | Ran a full TCP scan with Nmap scripts and version detection | Identified AD, SMB, RDP, WinRM, IIS, and MSSQL services. |

## Host inventory

| IP | Host | Domain | Role / notable services |
| --- | --- | --- | --- |
| 192.168.56.10 | KINGSLANDING | sevenkingdoms.local | Domain controller; DNS, Kerberos, LDAP/LDAPS, SMB, RDP, WinRM, IIS |
| 192.168.56.11 | WINTERFELL | north.sevenkingdoms.local | Domain controller; DNS, Kerberos, LDAP/LDAPS, SMB, RDP, WinRM |
| 192.168.56.12 | MEEREEN | essos.local | Domain controller; DNS, Kerberos, LDAP/LDAPS, SMB, RDP, WinRM |
| 192.168.56.22 | CASTELBLACK | north.sevenkingdoms.local | IIS, SMB, MSSQL, RDP, WinRM |
| 192.168.56.23 | BRAAVOS | essos.local | IIS, SMB, MSSQL, RDP, WinRM |

## Findings

- SMB signing was not required on `CASTELBLACK` and `BRAAVOS`.
- `BRAAVOS` reported SMB signing disabled and SMBv1 enabled.
- `MEEREEN` also reported SMBv1 enabled.
- `CASTELBLACK` and `BRAAVOS` exposed Microsoft SQL Server on TCP/1433.
- IIS was exposed on `KINGSLANDING`, `CASTELBLACK`, and `BRAAVOS`; Nmap reported the HTTP `TRACE` method.

## Detection and mitigation notes

- Require SMB signing and disable SMBv1 where legacy support is not required.
- Restrict and monitor MSSQL access; review SQL Server authentication and service-account configuration.
- Disable unnecessary HTTP methods such as `TRACE`.
- Monitor broad SMB and service-discovery activity in endpoint and network telemetry.

## ATT&CK mapping

| Technique | Evidence |
| --- | --- |
| T1046 – Network Service Discovery | Full TCP service scan of the authorized GOAD hosts. |

## References

- https://mayfly277.github.io/posts/GOADv2-pwning_part1/
