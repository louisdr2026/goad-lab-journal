# 06 — Lessons Learned

## 1. Enumerate Before Attempting a Relay

SMB signing enumeration identified CASTELBLACK and BRAAVOS as relay candidates before the relay attempt. Establishing the target surface first made the subsequent test focused and evidence-based.

## 2. Network Position Matters

Responder was bound to `eth1`, the interface connected to the GOAD network. Name-resolution poisoning depends on observing and influencing relevant traffic in an appropriate network position.

## 3. Poisoning and Relaying Are Separate Functions

Responder handled LLMNR/mDNS poisoning. Its SMB and HTTP listeners were disabled so that Impacket `ntlmrelayx` could handle the relay listeners. Coordinating these components was necessary for the demonstrated workflow.

## 4. Capturing Authentication Is Not the Same as Cracking a Password

The exercise observed NTLMv2 authentication and successfully relayed the authentication associated with `NORTH\robb.stark`. Offline password cracking was not required for this demonstration.

## 5. Successful Authentication Does Not Prove Administrative Access

The relay output confirmed successful SMB authentication against CASTELBLACK and BRAAVOS and showed SOCKS session registration. It did not establish administrator privileges, remote code execution, or full host compromise.

## 6. SMB Signing Is an Important Mitigation

SMB signing enforcement helps prevent NTLM relay attacks against SMB. Administrators should verify effective enforcement on both servers and clients and investigate systems where signing is disabled.

## 7. Reduce Unnecessary Name-Resolution and NTLM Exposure

Disabling LLMNR where unnecessary, limiting NetBIOS name resolution where feasible, reducing NTLM usage, and preferring Kerberos can reduce opportunities for credential interception and relay.

## 8. Preserve Evidence Safely

Raw Responder and relay logs may contain sensitive authentication material. Keeping raw evidence outside the Git repository and documenting only sanitized findings reduces the risk of exposing credentials or challenge-response data.

## 9. Keep Claims Proportional to Evidence

The final report distinguishes demonstrated outcomes from untested possibilities. The proven result was successful NTLM relay to two SMB targets; privilege escalation, credential dumping, persistence, and remote code execution were not demonstrated.

## Conclusion

Part 4 demonstrated the relationship between name-resolution poisoning, NTLM authentication, SMB signing configuration, and NTLM relay. The key defensive lessons are to enforce SMB signing, reduce unnecessary name-resolution protocols and NTLM usage, and monitor authentication flows for suspicious behavior.
