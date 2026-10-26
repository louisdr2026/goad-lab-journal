# 01 — Relay Surface

## Objective

Identify GOAD hosts with SMB signing disabled and generate a target list for NTLM relay.

SMB signing is important because NTLM relay attacks depend on being able to forward an authenticated NTLM session to another SMB service without the target requiring a valid cryptographic signature.

## Network Scope

- Network: `192.168.56.0/24`
- Scan range: `192.168.56.10-23`
- Attacker interface: `eth1`
- Attacker IP: `192.168.56.133`

## SMB Signing Enumeration

The following NetExec command was used:

```bash
nxc smb 192.168.56.10-23 --gen-relay-list relay-targets.txt
```

### Command breakdown

- `nxc` — NetExec, used for network service enumeration and authentication testing.
- `smb` — selects the SMB protocol module.
- `192.168.56.10-23` — scans the specified range of GOAD hosts.
- `--gen-relay-list relay-targets.txt` — writes hosts identified as suitable NTLM relay targets to the specified file.

## Results

The responsive hosts included the following SMB signing states:

| Host | IP Address | SMB Signing | Relay Candidate |
|---|---|---|---|
| WINTERFELL | `192.168.56.11` | Enabled | No |
| KINGSLANDING | `192.168.56.10` | Enabled | No |
| MEEREEN | `192.168.56.12` | Enabled | No |
| CASTELBLACK | `192.168.56.22` | Disabled | Yes |
| BRAAVOS | `192.168.56.23` | Disabled | Yes |

Only CASTELBLACK and BRAAVOS were written to the relay target list.

The resulting target file contained:

```text
192.168.56.22
192.168.56.23
```

## Baseline Verification

A separate SMB enumeration was also performed to preserve the broader signing baseline:

```bash
nxc smb 192.168.56.10-23
```

This confirmed that the relay targets were not selected arbitrarily; they were identified from the SMB signing configuration observed during enumeration.

## Security Significance

The presence of SMB hosts with signing disabled created a viable NTLM relay surface within the lab.

The attack path therefore became:

```text
Identify unsigned SMB
        ↓
Generate relay target list
        ↓
Poison name resolution
        ↓
Capture NTLM authentication
        ↓
Relay authentication to unsigned SMB
```

This enumeration step was critical because NTLM relay requires an appropriate target. Hosts enforcing SMB signing were excluded from the generated relay list.

## Evidence

Raw command output and relay-target files are retained under the private evidence directory:

`~/goad-evidence/part-04`

No credentials, NTLM hashes, or other sensitive authentication material are included in this documentation.
