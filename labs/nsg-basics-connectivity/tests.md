# Tests – NSG Basics Connectivity

## Variables
- WEB_IP = `________`
- DB_IP  = `________`

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
curl -m 3 http://WEB_IP
```

Result:
- Expected: OK
- Actual: ________

## Step 2 – Priorities proof
On `db`:
```bash
curl -m 3 http://WEB_IP
```

Result:
- Expected: OK when Allow(100) + Deny(200)
- Actual: ________

Optional negative test (Deny prio 50):
- Expected: FAIL
- Actual: ________

## Step 3 – Outbound denyall impact
On `db`:
```bash
curl -m 3 http://WEB_IP || echo "VNet blocked"
curl -I https://example.com || echo "Internet blocked"
```

Result:
- Expected: VNet FAIL + Internet FAIL
- Actual: ________

After Allow VirtualNetwork (prio 100):
```bash
curl -m 3 http://WEB_IP
curl -I https://example.com || echo "Internet still blocked"
```

Result:
- Expected: VNet OK + Internet FAIL
- Actual: ________

Optional Allow Internet 443 (prio 110):
```bash
curl -I https://example.com
```

Result:
- Expected: OK
- Actual: ________

## Step 4 – ICMP ping
On `db`:
```bash
ping -c 2 WEB_IP
```

Result:
- Expected: OK (with ICMP allow)
- Actual: ________
