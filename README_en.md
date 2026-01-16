# ZoqueLabs — OLIM Datasets

> Observatorio Latinoamericano de Infraestructura Maliciosa

OLIM URL: https://zoquelabs.xyz/OLIM (current snapshot)


This repository contains artifacts generated from periodic snapshots of
suspected command-and-control (C2) and malicious infrastructure observed
in Latin America. Check the information gathering and info processing 
scripts in this repository: https://github.com/ZoqueLabs/frida_data_handler

---

## What this is

- A **snapshot-based view** of malicious / suspicious network infrastructure
- Focused on **Latin America**
- Built to support:
  - threat-intelligence research
  - longitudinal analysis (infra reuse, churn, drift)
  - future ingestion into TI platforms (MISP / STIX / Colander)

Each snapshot is treated as a *moment in time*, not as live telemetry.

---

## Input data (snapshot JSON)

Each snapshot is a JSON mapping of IP addresses to observed metadata:

```json
{
  "X.X.X.X": {
    "threat_names": ["Cobalt Strike", "Havoc"],
    "ports": [443, 31337],
    "asn": "AS16509",
    "org": "Amazon Data Services",
    "isp": "Amazon.com, Inc.",
    "country": "Brazil",
    "city": "São Paulo",
    "last_scan": "2026-01-02_16-19-23",
    "source": "censys"
  }
}
````

Notes:

* One IP may be associated with **multiple threat frameworks**
* Ports represent **observed open ports at scan time**
* Geo, ASN, ISP, and org strings are preserved *as observed* (no forced normalization)

---

## Generated outputs

Running the report script produces the following files:

### `report.md`

Human-readable technical report, including:

* Snapshot metadata
* Global metrics (IPs, ports, threats, countries, ASNs…)
* **Top-N lists** (compact, screen-friendly)
* **Mermaid graphs**:

  * Threat distribution
  * Country / ASN / ISP distributions
  * Country → Threat and ASN → Threat relationships
* **Delta section** (if previous snapshot provided):

  * New / removed IPs
  * New / removed malware frameworks
  * New countries, ASNs, ISPs, ports
  * IP reuse and threat drift
  * Delta-only Sankey graphs
* **Complete table of all IPs** in the current snapshot (at the bottom)

The report is designed to be viewable directly in
GitHub, GitLab, or Markdown editors supporting Mermaid.

---

### `summary.json`

Machine-readable summary:

* Snapshot timestamp
* Core metrics
* Top-N values for threats, countries, ASNs, ISPs, ports
* Delta information (when previous snapshot is provided)
* IP reuse / threat drift details

This file is suitable for:

* dashboards
* further automated processing
* downstream scripts

---

### `dataset.csv`

Flat dataset (one row per IP), suitable for spreadsheets or quick filtering:

Columns:

* `ip`
* `country`
* `city`
* `asn`
* `isp`
* `org`
* `ports` (semicolon-separated)
* `threat_names` (semicolon-separated)
* `source`
* `last_scan`

This is intended for **exploration**, not enrichment.

---

### `stix2_bundle.json`

Minimal **STIX 2.1** bundle export:

* One `identity` object (organization)
* One `indicator` per IP

  * Pattern: `[ipv4-addr:value = 'X.X.X.X']`
  * Labels include `c2`, `threat-infra`, and `malware:<name>`

This export is intentionally conservative:
valid STIX, easy to ingest, no over-modeled relationships.

---

### `misp_event.json`

Minimal **MISP Event** export:

* Single Event
* One `ip-dst` attribute per IP
* Optional `port` attributes
* Malware names exposed as tags (`malware:<name>`)
* Contextual metadata (country, ASN, ISP, source) stored as comments

Designed for portability and easy import into existing MISP instances.

---

## Snapshot comparison (delta logic)

When a previous snapshot is provided, the report computes:

* **New IPs** and **removed IPs**
* **Persistent IPs**
* **New and removed threat frameworks**
* **New countries, ASNs, ISPs, ports**
* **IP reuse / threat drift**

### IP reuse definition

An IP is considered reused when:

> The same IP appears in both snapshots **but with a different set of associated threat frameworks**.

This captures:

* infrastructure reuse
* tooling rotation
* operator staging changes

It does **not** imply attribution.

---

## What this is *not*

* Not a real-time monitoring system
* Not a scanner
* Not an attribution engine
* Not claiming control or intent

This is **observational threat-infrastructure research**.

---

## Intended use

* Technical reporting
* Longitudinal TI analysis
* Research publications
* Civil-society threat labs
* Capacity building in the region

---

## License / usage

Unless stated otherwise:

* Data is observational
* Interpret carefully
* Avoid over-claiming
* Cite responsibly

ZoqueLabs

