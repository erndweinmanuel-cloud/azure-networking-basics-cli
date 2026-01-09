# NSG Basics – Connectivity (Priorities, Inter-VM, Outbound, ICMP)

Goal: Prove how NSG rule evaluation works in practice (AZ-104 networking fundamentals).

## Lab Scope
- Priorities / first-match-wins
- Inter-VM communication (same VNet)
- Outbound rules (DenyAll impact + selective allows)
- ICMP (ping) allow/deny

## Environment
- Resource Group: `rg-nsg-lab`
- Region: `westeurope`
- VNet: `vnet-nsg-lab` (Address space: `10.0.0.0/16`)
- Subnets:
  - web-subnet: `10.0.0.0/24` (Subnet NSG: `nsg-web`)
  - db-subnet:  `10.0.1.0/24` (Subnet NSG: `nsg-db`)
- VMs (Linux):
  - `web` (WEB_IP: `10.0.0.4`)
  - `db`  (DB_IP:  `10.0.1.4`)
- Access:
  - SSH to DB used for testing (public IP existed during lab)
  - Tests executed on DB via SSH + curl/ping

## Step 0 – Prepare workload
On `web`:
```bash
sudo apt update
sudo apt install -y nginx
sudo systemctl enable --now nginx
```

## Step 1 – Inter-VM baseline (Video 84)
On `db`:
```bash
curl -m 3 -I http://10.0.0.4
```

Expected:
- OK (nginx response)

Actual:
- [x] OK (HTTP/1.1 200 OK)

Proof:
- `proofs/nsg-basics-connectivity/01_intervm_curl.txt`

## Step 2 – Priorities / rule order proof (Video 83)
Target: `nsg-web` (Inbound, subnet scope)

Rules (Inbound):
1) Allow TCP 80 from DB -> WEB (Priority 100) ✅
2) Deny  TCP 80 from VirtualNetwork -> WEB (Priority 200) ✅

Test on `db`:
```bash
curl -m 3 -I http://10.0.0.4
```

Expected:
- OK (Allow 100 wins)

Actual:
- [x] OK (HTTP/1.1 200 OK)

Proof:
- `proofs/nsg-basics-connectivity/02_web_inbound_rules.txt`
- `proofs/nsg-basics-connectivity/03_priority_allow_wins.txt`

## Step 3 – Outbound rules / DenyAll impact (Video 85)
Target: `nsg-db` (Outbound, subnet scope)

Rules (Outbound):
1) DenyAllTraffic Any->Any (Priority 400) ✅
2) Allow VirtualNetwork (Priority 100) ✅

Tests on `db` (DenyAll only):
```bash
curl -m 3 -I http://10.0.0.4 >/dev/null 2>&1 && echo VNet_OK || echo VNet_blocked
curl -m 5 -I https://example.com >/dev/null 2>&1 && echo Internet_OK || echo Internet_blocked
```

Actual (DenyAll only):
- VNet_blocked ✅
- Internet_blocked ✅

Re-test after Allow VirtualNetwork (prio 100):
- VNet_OK ✅
- Internet_blocked ✅

Proof:
- `proofs/nsg-basics-connectivity/04_db_outbound_rules.txt`
- `proofs/nsg-basics-connectivity/05_outbound_denyall_fail.txt`
- `proofs/nsg-basics-connectivity/06_allow_vnet_ok.txt`

## Step 4 – ICMP / ping (Video 86)
Target: `nsg-web` inbound (subnet scope)

Rule (Inbound):
- Allow ICMP from DB -> WEB (Priority 120) ✅

Test on `db`:
```bash
ping -c 2 10.0.0.4 && echo PING_OK || echo PING_BLOCKED
```

Actual:
- [x] PING_OK (2/2 received)

Proof:
- `proofs/nsg-basics-connectivity/07_icmp_allow_ping_ok.txt`

## Results / Key takeaways
- Lowest priority number is evaluated first (first match wins).
- Subnet NSG + NIC NSG both apply; easiest lab setup is to keep logic in Subnet NSGs.
- Outbound DenyAll (Any->Any) blocks both Internet and VNet unless VirtualNetwork is explicitly allowed.
- ICMP is separate from TCP/UDP and must be explicitly allowed for ping.

## Next step
- Two-tier architecture lab (web -> db on TCP 3306) + Application Security Groups (ASG)

## Files
- Rules: `rules.md`
- Tests: `tests.md`
- Notes: `../../notes/2026-01-09_nsg-basics-connectivity.md`
