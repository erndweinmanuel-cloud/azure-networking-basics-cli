# Tests

## Test 1 – Internet blocked from DB
Command (RunCommand or SSH):
curl -m 5 -I https://example.com >/dev/null 2>&1 && echo Internet_OK || echo Internet_blocked

Expected: Internet_blocked
Actual: Internet_blocked
Proof: proofs/two-tier-architecture/05_test_db_internet_blocked.png

## Test 2 – Internet blocked from Web
curl -m 5 -I https://example.com >/dev/null 2>&1 && echo Internet_OK || echo Internet_blocked

Expected: Internet_blocked
Actual: Internet_blocked
Proof: proofs/two-tier-architecture/06_test_web_internet_blocked.png
