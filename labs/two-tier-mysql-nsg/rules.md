# NSG Rules

## Web NSG (nsg-web) – Inbound
- Allow SSH from MYIP -> 22 (prio 100)
- Allow HTTP from Internet -> 80 (prio 110)

## DB NSG (nsg-db) – Inbound
- Allow SSH from web subnet (10.0.0.0/24) -> 22 (prio 100)
- Allow MySQL from web VM (10.0.0.4) -> 3306 (prio 110)
- Deny MySQL from Internet -> 3306 (prio 400)

## Outbound (both NSGs)
- Deny outbound to Internet (prio 400)
