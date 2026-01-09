# NSG Rules – NSG Basics Connectivity

> Fill in the exact NSG name(s) and where attached (Subnet or NIC).

## Attachments
- Web NSG name: `nsg-________` | Attached to: `[ ] web-subnet  [ ] web NIC`
- DB  NSG name: `nsg-________` | Attached to: `[ ] db-subnet   [ ] db NIC`

## Inbound rules (Web side)
| Priority | Action | Protocol | Source | Source Port | Destination | Dest Port | Notes |
|---:|---|---|---|---|---|---|---|
| 100 | Allow | TCP | `DB_IP` or `db-subnet` | * | `web` | 80 | Allow HTTP from DB |
| 200 | Deny  | TCP | `VirtualNetwork` | * | `web` | 80 | Deny HTTP from VNet (priority test) |
| 120 | Allow | ICMP | `DB_IP` or `db-subnet` | * | `web` | * | Allow ping (ICMP) |

## Outbound rules (DB side)
| Priority | Action | Protocol | Source | Source Port | Destination | Dest Port | Notes |
|---:|---|---|---|---|---|---|---|
| 100 | Allow | Any | `db` | * | `VirtualNetwork` | * | Re-enable VNet after DenyAll |
| 110 | Allow | TCP | `db` | * | `Internet` | 443 | Optional: allow HTTPS outbound |
| 400 | Deny  | Any | `db` | * | `Any` | * | DenyAllTraffic (demonstrate impact) |

## Notes
- If you used Service Tags: `VirtualNetwork`, `Internet` write them explicitly above.
- If you used Subnet NSGs, remember they affect all NICs in that subnet.
