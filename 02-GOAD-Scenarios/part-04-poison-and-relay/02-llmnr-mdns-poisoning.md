# 02 — LLMNR/mDNS Poisoning

## Objective

Use Responder to poison LLMNR and mDNS name-resolution requests within the GOAD network and observe whether hosts initiate NTLM authentication toward the attacker.

## Responder Configuration

Responder was started on the GOAD-facing interface:

```bash
sudo responder -I eth1 -v
```

### Command breakdown

- `sudo` — runs Responder with the privileges required to bind to network services.
- `responder` — starts the Responder name-resolution poisoning framework.
- `-I eth1` — binds Responder to the GOAD lab interface.
- `-v` — enables verbose output for observing poisoning and authentication events.

## Observed Poisoning

Responder observed name-resolution requests originating from GOAD systems, including WINTERFELL, CASTELBLACK, and KINGSLANDING.

Two poisoning events from WINTERFELL resulted in observable SMB NTLMv2 authentication:

| Source | Resolution Request | Authentication |
|---|---|---|
| WINTERFELL (`192.168.56.11`) | `Meren.local` / `Meren` | SMB NTLMv2-SSP |
| WINTERFELL (`192.168.56.11`) | `Bravos.local` / `Bravos` | SMB NTLMv2-SSP |

The associated accounts were observed as:

- `NORTH\eddard.stark`
- `NORTH\robb.stark`

Raw NTLM challenge-response material is intentionally excluded from this repository.

## Why the Poisoning Worked

The observed behavior demonstrates that a GOAD host attempted to resolve names using protocols that do not provide the same authentication protections as properly configured DNS.

Responder answered the poisoned requests and became the destination for the resulting authentication attempt.

The resulting sequence was:

```text
WINTERFELL
    ↓
LLMNR / mDNS request
    ↓
Responder poisoning response
    ↓
SMB authentication attempt
```

## Responder and ntlmrelayx Coordination

For the relay stage, Responder SMB and HTTP listeners were disabled so that `ntlmrelayx` could provide the required listeners.

The relevant Responder configuration was changed from enabled to disabled for:

- SMB
- HTTP

The configuration was verified before restarting Responder.

This separation allowed Responder to continue performing name-resolution poisoning while `ntlmrelayx` handled the relay listeners.

## Evidence Handling

Raw Responder output was retained in the private evidence directory:

`~/goad-evidence/part-04`

The GitHub documentation deliberately records only:

- Source host
- Protocol
- Authentication type
- Account name
- Relevant poisoning request

Credential hashes, challenge-response material, passwords, and other reusable authentication secrets are not documented.

## Security Significance

LLMNR and mDNS poisoning can create an opportunity for an attacker positioned on the same network segment to induce authentication toward an attacker-controlled system.

In this lab, that authentication became the input for the NTLM relay stage documented in the following sections.
