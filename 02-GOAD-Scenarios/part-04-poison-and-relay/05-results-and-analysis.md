# 05 — Results and Analysis

## Executive Summary

GOAD Part 4 demonstrated an NTLM relay attack using LLMNR/mDNS poisoning, Responder, and Impacket ntlmrelayx.

SMB signing enumeration identified two relay candidates. Authentication observed from WINTERFELL was successfully relayed to both targets.

## Findings

| ID | Finding | Evidence |
|---|---|---|
| F-01 | SMB signing was disabled on two identified targets | NetExec SMB signing enumeration |
| F-02 | Name-resolution poisoning induced NTLMv2 SMB authentication | Responder output |
| F-03 | Authentication from NORTH\robb.stark was successfully relayed to CASTELBLACK | ntlmrelayx reported successful authentication |
| F-04 | Authentication from NORTH\robb.stark was successfully relayed to BRAAVOS | ntlmrelayx reported successful authentication |

## Attack Chain

```text
SMB signing enumeration
        ↓
Identify CASTELBLACK and BRAAVOS
        ↓
LLMNR/mDNS poisoning with Responder
        ↓
Observe NTLMv2 authentication from WINTERFELL
        ↓
Relay NORTH\robb.stark authentication
        ↓
Successful SMB authentication to both targets
```

## Confirmed Results

- Two NTLMv2-SSP SMB authentication events were observed from WINTERFELL.
- The authentication associated with NORTH\robb.stark was relayed successfully.
- CASTELBLACK (`192.168.56.22`) accepted the relayed SMB authentication.
- BRAAVOS (`192.168.56.23`) accepted the relayed SMB authentication.
- ntlmrelayx registered SOCKS sessions for both targets.

## Results Not Established

Successful relay authentication does not establish the level of access available through the resulting sessions.

This exercise did not demonstrate:

- Local administrator or Domain Administrator privileges
- Privilege escalation
- Remote code execution
- Credential dumping
- Persistence or domain compromise

These outcomes must not be claimed without additional evidence.

## Risk Interpretation

The lab demonstrates that hosts without SMB signing enforcement can be susceptible to NTLM relay when an attacker can induce or intercept NTLM authentication and reach the target service.

The practical impact depends on the relayed identity, target configuration, available permissions, and enabled protections. SMB signing is an important mitigation for SMB relay, but it does not eliminate every form of NTLM relay against every protocol.

## Recommended Defensive Controls

1. Enforce SMB signing on servers and clients wherever supported.
2. Disable LLMNR where it is not required, using managed Group Policy settings.
3. Disable NetBIOS name resolution where operationally feasible.
4. Reduce or eliminate NTLM authentication where compatibility permits; prefer Kerberos.
5. Apply appropriate Extended Protection for Authentication and channel binding to supported services.
6. Monitor for anomalous name-resolution responses and NTLM authentication flows.
7. Review SMB signing policy and verify enforcement rather than relying only on configuration intent.

## Evidence Location

Raw logs and detailed evidence are retained outside the repository under `~/goad-evidence/part-04`.

This document contains sanitized results only. No raw challenge-response values, hashes, or passwords are included.
