# Two-Tier Architecture (NSG) – Web + DB

## Goal
Build a simple two-tier setup:
- Web VM is reachable from the Internet on **HTTP (80)** (and SSH restricted to my IP).
- DB VM (MySQL 3306) is **not reachable from the Internet**.
- Only the Web VM can reach the DB on **3306**.
- Optional hardening: block **outbound Internet** from both VMs.

## Architecture
- VNet: `vnet-two-tier`
- Subnets:
  - `web-subnet` 10.0.0.0/24
  - `db-subnet`  10.0.1.0/24
- VMs:
  - `web` (10.0.0.4)
  - `db`  (10.0.1.4)

## Proofs (screenshots)
- IPs: `proofs/two-tier-architecture/01_ips_web_db.png`
- NSG Web inbound: `proofs/two-tier-architecture/02_nsg_web_inbound_rules.png`
- NSG DB inbound: `proofs/two-tier-architecture/03_nsg_db_inbound_rules.png`
- Outbound deny Internet: `proofs/two-tier-architecture/04_nsg_outbound_deny_internet.png`
- DB Internet blocked: `proofs/two-tier-architecture/05_test_db_internet_blocked.png`
- Web Internet blocked: `proofs/two-tier-architecture/06_test_web_internet_blocked.png`

## Files
- `rules.md` – NSG rules overview
- `tests.md` – test commands + expected/actual results
