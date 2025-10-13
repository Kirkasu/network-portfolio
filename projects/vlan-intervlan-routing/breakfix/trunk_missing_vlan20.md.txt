# Break/Fix Scenario: VLAN 20 Missing from Trunk

**Date:** 2025-10-13  
**Project:** VLAN & Inter-VLAN Routing (ROAS)  
**Status:** ✅ Resolved  

---

## Objective
Show how removing VLAN 20 from the SW1↔R1 trunk breaks inter-VLAN connectivity, and how to fix it.

---

## Lab Context
| Device | Interface(s) | Role | Notes |
|--------|---------------|------|-------|
| R1 | G0/0.10 / G0/0.20 | Router-on-a-Stick | 192.168.10.1 / 192.168.20.1 |
| SW1 | G0/0 (trunk), G0/1, G0/2 | L2 Switch | G0/1 → VLAN10 • G0/2 → VLAN20 |
| PC1 | eth0 (VLAN10) | Host | 192.168.10.100/24, GW 192.168.10.1 |
| PC2 | eth0 (VLAN20) | Host | 192.168.20.100/24, GW 192.168.20.1 |

---

## Problem Introduced
On SW1, VLAN 20 was accidentally removed from the trunk:
```bash
SW1(config)# interface g0/0
SW1(config-if)# switchport trunk allowed vlan 10
SW1(config-if)# end
```

---

## Observed Symptoms (Before Fix)
- PC1 can ping its gateway (192.168.10.1)
- PC2 cannot ping its gateway (192.168.20.1)
- PC1 cannot reach PC2
- R1 subinterfaces are up/up (so Layer 3 is fine)

**Before Evidence**
```bash
PC1> ping 192.168.20.100
* * * * *   (Request timed out)
```

```bash
SW1# show interfaces trunk
Port      Mode  Encapsulation  Status    Native vlan
Gi0/0     on    802.1q         trunking  1
Port Vlans allowed on trunk
Gi0/0     10
```

---

## Root Cause
VLAN 20 tags are not permitted on the SW1→R1 trunk, so frames for VLAN 20 never reach R1’s G0/0.20 subinterface.

---

## Fix Implemented
Re-allow VLAN 20 on the trunk:
```bash
SW1(config)# interface g0/0
SW1(config-if)# switchport trunk allowed vlan 10,20
SW1(config-if)# end
```

---

## Verification (After Fix)
```bash
SW1# show interfaces trunk
Port Vlans allowed on trunk
Gi0/0     10,20
```

```bash
PC1> ping 192.168.20.100
84 bytes from 192.168.20.100 icmp_seq=1 ttl=63 time=1.1 ms
84 bytes from 192.168.20.100 icmp_seq=2 ttl=63 time=1.0 ms
```

```bash
R1# show ip interface brief | include 0/0
GigabitEthernet0/0.10   192.168.10.1   up   up
GigabitEthernet0/0.20   192.168.20.1   up   up
```

---

## Lessons Learned
- Always verify allowed VLANs on trunk links (`show interfaces trunk`).
- Troubleshooting order that works:
  1. `show vlan brief` → access VLANs  
  2. `show interfaces trunk` → tags carried  
  3. `show ip int brief` → router subinterfaces  
- Keep before/after captures for proof.

---

## Evidence Files
- captures/troubleshooting/2025-10-13_vlan-20-missing-before.txt  
- captures/troubleshooting/2025-10-13_vlan-20-missing-after.txt
