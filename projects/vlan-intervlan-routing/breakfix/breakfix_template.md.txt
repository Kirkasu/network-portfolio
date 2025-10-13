# Break/Fix Scenario: <Short Title Here>

## Objective
Explain what you intentionally broke or what issue appeared naturally.
Example: “Verify that VLANs not allowed on the trunk will block inter-VLAN communication.”

---

## Lab Context
| Device | Interface | Role | Notes |
|---------|------------|------|-------|
| R1 | G0/0.10 / .20 | L3 Gateway | Router-on-a-stick |
| SW1 | G0/0 | Trunk to R1 | Carries VLAN 10 and 20 |
| PC1 | eth0 VLAN10 | Host 1 | 192.168.10.100/24 |
| PC2 | eth0 VLAN20 | Host 2 | 192.168.20.100/24 |

---

## Problem Introduced
Show exactly what you changed (the “break”).
```bash
SW1(config-if)#switchport trunk allowed vlan 10
