# GOAD Lab Journal

A personal, evidence-based record of my authorized work in the **Game of Active Directory (GOAD)** lab.

## Purpose

This repository documents lab reproductions, observations, detection notes, and controlled experiments. GOAD documentation is the scenario reference; this journal records my own commands, results, evidence, and lessons learned.

## Scope and safety

Only use these notes and techniques in systems you own or are explicitly authorized to test. Do not commit passwords, hashes, tokens, private keys, VPN profiles, or personally identifying data.

## Journal map

| Folder | Contents |
| --- | --- |
| `00-Environment/` | Sanitized topology, hosts, domains, and tool notes |
| `01-Reconnaissance/` | Discovery and enumeration notes |
| `02-GOAD-Scenarios/` | One folder per GOAD scenario reproduction |
| `03-My-Experiments/` | Clearly separated, controlled follow-up experiments |
| `04-Detection/` | Telemetry, detections, and defensive observations |
| `screenshots/` | Supporting evidence organized by activity |
| `scripts/` | Safe helper scripts; never secrets |
| `reports/` | Assessment summaries and final report drafts |
| `references/` | Links and citation notes, not copied third-party material |

## How I document a scenario

1. Link the relevant GOAD reference.
2. Record the objective and authorization boundary.
3. Log commands I actually ran and their sanitized output.
4. Add evidence, observations, impact, and detection opportunities.
5. Map techniques to MITRE ATT&CK only where the evidence supports it.

Start a new reproduction by copying `02-GOAD-Scenarios/_template/` to a clearly named folder, then complete `README.md` inside it.

## Status

- Lab: already running (details intentionally sanitized)
- Current focus: `<add current activity>`
- Last updated: `<YYYY-MM-DD>`
