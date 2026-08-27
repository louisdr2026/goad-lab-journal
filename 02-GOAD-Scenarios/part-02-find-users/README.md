# GOAD Scenario: Part 02 — Find Users

**Status:** reproduced  
**Date:** 2026-08-27  
**GOAD reference:** https://mayfly277.github.io/posts/GOADv2-pwning-part2/  
**Authorization/scope:** Authorized, isolated VMware GOAD lab.

## Objective

Identify domain users without credentials, assess password policy, and test for accounts that do not require Kerberos pre-authentication.

## Execution summary

| Step | Action | Result |
| --- | --- | --- |
| 1 | Anonymous SMB/RPC user enumeration against `WINTERFELL` | Enumerated 10 accounts in the `NORTH` domain. |
| 2 | Retrieved the domain password policy | Found a minimum password length of 5, disabled complexity, and a five-attempt lockout threshold. |
| 3 | Reviewed user metadata | Identified a password-like value exposed in an account description; validation did not succeed. |
| 4 | Tested discovered usernames for AS-REP roasting | Identified one account configured without Kerberos pre-authentication. |
| 5 | Performed an offline password audit against the captured AS-REP material | A valid credential was recovered locally; the value is intentionally excluded from this repository. |

## Findings

- Anonymous user enumeration disclosed account names and descriptions.
- Account metadata exposed sensitive password-like information.
- Weak password requirements increase credential-guessing risk.
- One account had Kerberos pre-authentication disabled, allowing AS-REP material to be requested without prior authentication.

## Security impact

An attacker with network access could enumerate users anonymously and obtain credential material for offline password auditing. Together with weak password controls, this can lead to an initial domain-user foothold.

## Detection and mitigation notes

- Disable anonymous enumeration where it is not required.
- Never store passwords or secrets in account descriptions.
- Require Kerberos pre-authentication for all user accounts unless a documented exception exists.
- Enforce stronger password length and complexity requirements.
- Monitor Kerberos ticket-request activity for unusual volumes or accounts configured without pre-authentication.

## ATT&CK mapping

| Technique | Evidence |
| --- | --- |
| T1087.002 – Domain Account Discovery | Anonymous enumeration returned domain accounts. |
| T1558.004 – AS-REP Roasting | An account without Kerberos pre-authentication returned AS-REP material. |

## Evidence handling

Raw tool output, hashes, and recovered credentials are stored only in the private evidence archive and are excluded from GitHub.
