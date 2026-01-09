# Tests – NSG Basics Connectivity

## Variables
- WEB_IP = `10.0.0.4`
- DB_IP  = `10.0.1.4`

## Step 0 – Web workload
On `web`:
```bash
sudo apt update
sudo apt install -y nginx
sudo systemctl enable --now nginx
```

## Step 1 – Inter-VM baseline
On `db`:
```bash
curl -m 3 -I http://10.0.0.4
```

Result:
- Expected: OK
- Actual: `HTTP/1.1 200 OK`

## Step 2 – Priorities proof
Rules on `nsg-web`:
- allow-http-from-db prio 100
- deny-http-from-vnet prio 200

On `db`:
```bash
curl -m 3 -I http://10.0.0.4
```

Result:
- Expected: OK when Allow(100) + Deny(200)
- Actual: `HTTP/1.1 200 OK`

## Step 3 – Outbound denyall impact
Rules on `nsg-db`:
- denyall-outbound prio 400 (Deny Any->Any)
- allow-vnet-outbound prio 100 (Allow to VirtualNetwork)

On `db` (DenyAll only):
```bash
curl -m 3 -I http://10.0.0.4 >/dev/null 2>&1 && echo VNet_OK || echo VNet_blocked
curl -m 5 -I https://example.com >/dev/null 2>&1 && echo Internet_OK || echo Internet_blocked
```

Result:
- Expected: VNet FAIL + Internet FAIL
- Actual: `VNet_blocked` + `Internet_blocked`

After Allow VirtualNetwork (prio 100):
```bash
curl -m 3 -I http://10.0.0.4 >/dev/null 2>&1 && echo VNet_OK || echo VNet_blocked
curl -m 5 -I https://example.com >/dev/null 2>&1 && echo Internet_OK || echo Internet_blocked
```

Result:
- Expected: VNet OK + Internet FAIL
- Actual: `VNet_OK` + `Internet_blocked`

## Step 4 – ICMP ping
Rule on `nsg-web`:
- allow-icmp-from-db prio 120

On `db`:
```bash
ping -c 2 10.0.0.4 && echo PING_OK || echo PING_BLOCKED
```

Result:
- Expected: OK (with ICMP allow)
- Actual: `PING_OK` (2 transmitted, 2 received)
