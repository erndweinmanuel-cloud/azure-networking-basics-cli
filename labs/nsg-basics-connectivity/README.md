# NSG Basics – Connectivity (Priorities, Inter-VM, Outbound, ICMP)

Goal: Prove how NSG rule evaluation works in practice (AZ-104 networking fundamentals).

## Lab Scope
- Priorities / first-match-wins
- Inter-VM communication (same VNet)
- Outbound rules (DenyAll impact + selective allows)
- ICMP (ping) allow/deny

## Environment
- Resource Group: `rg-________`
- VNet: `vnet-________` (Address space: `10.0.0.0/16`)
- Subnets:
  - web-subnet: `10.0.0.0/24`
  - db-subnet:  `10.0.1.0/24`
- VMs (Linux):
  - `web` (WEB_IP: `10.0.0.__`)
  - `db`  (DB_IP:  `10.0.1.__`)
- Access: SSH (no Bastion required for this lab)

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
curl -m 3 http://WEB_IP
```

Expected:
- OK (nginx response)

Actual:
- [ ] OK
- [ ] FAIL (reason: ________)

Proof:
- `proofs/nsg-basics-connectivity/01_intervm_curl.png`

## Step 2 – Priorities / rule order proof (Video 83)
Target: `web` inbound NSG (Subnet or NIC)

Rules (Inbound):
1) Allow TCP 80 from DB -> WEB (Priority 100)
2) Deny  TCP 80 from VirtualNetwork -> WEB (Priority 200)

Test on `db`:
```bash
curl -m 3 http://WEB_IP
```

Expected:
- OK (Allow 100 wins)

Optional negative test:
- Change Deny priority to 50 -> expected FAIL

Proof:
- `proofs/nsg-basics-connectivity/02_web_inbound_rules.png`
- `proofs/nsg-basics-connectivity/03_priority_allow_wins.png`
- (optional) `proofs/nsg-basics-connectivity/04_priority_deny_wins.png`

## Step 3 – Outbound rules / DenyAll impact (Video 85)
Target: `db` outbound NSG (Subnet or NIC)

Rules (Outbound):
1) DenyAllTraffic Any->Any (Priority 400)

Tests on `db`:
```bash
curl -m 3 http://WEB_IP || echo "VNet blocked"
curl -I https://example.com || echo "Internet blocked"
```

Expected:
- VNet FAIL
- Internet FAIL

Fix:
2) Allow VirtualNetwork (Priority 100)

Re-test:
```bash
curl -m 3 http://WEB_IP
curl -I https://example.com || echo "Internet still blocked"
```

Expected:
- VNet OK
- Internet still FAIL

Optional:
3) Allow Internet 443 (Priority 110)
```bash
curl -I https://example.com
```
Expected:
- Internet OK

Proof:
- `proofs/nsg-basics-connectivity/05_db_outbound_rules.png`
- `proofs/nsg-basics-connectivity/06_outbound_denyall_fail.png`
- `proofs/nsg-basics-connectivity/07_allow_vnet_ok.png`
- (optional) `proofs/nsg-basics-connectivity/08_allow_internet_443_ok.png`

## Step 4 – ICMP / ping (Video 86)
Target: `web` inbound NSG

Rule (Inbound):
- Allow ICMP from DB -> WEB (Priority 120)

Test on `db`:
```bash
ping -c 2 WEB_IP
```

Expected:
- OK

Optional:
- Remove/deny ICMP -> expected FAIL

Proof:
- `proofs/nsg-basics-connectivity/09_icmp_allow_ping_ok.png`
- (optional) `proofs/nsg-basics-connectivity/10_icmp_deny_ping_fail.png`

## Results / Key takeaways
- Lowest priority number is evaluated first (first match wins).
- NSG evaluation is effectively “most restrictive wins” across NIC/Subnet scope.
- Outbound DenyAll (Any->Any) blocks both Internet and VNet unless VirtualNetwork is explicitly allowed.
- ICMP is separate from TCP/UDP and must be explicitly allowed for ping.

## Next step
- Two-tier architecture lab (web -> db on TCP 3306) + Application Security Groups (ASG)

## Files
- Rules: `rules.md`
- Tests: `tests.md`
- Notes: `../../notes/2026-01-09_nsg-basics-connectivity.md`
