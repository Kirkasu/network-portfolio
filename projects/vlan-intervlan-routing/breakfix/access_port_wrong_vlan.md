# Break/Fix: Access Port in the Wrong VLAN
**Date:** 2025-10-13  
**Status:** ✅ Resolved

---

## Objective
Show how putting PC1's access port in the wrong VLAN leads to failed reachability and fix it.

---

## Topology Context
- PC1 should be on **VLAN 10** (192.168.10.100/24).
- PC2 on **VLAN 20** (192.168.20.100/24).
- SW1 access ports: Gi0/1 → VLAN 10, Gi0/2 → VLAN 20.

---

## Problem Introduced
Place PC1's port (Gi0/1) into VLAN **20** by mistake.
```bash
SW1(config)# interface g0/1
SW1(config-if)# switchport access vlan 20
SW1(config-if)# end
```

---

## Observed Symptoms (Before Fix)
- PC1 reaches wrong default gateway (if any) or can't reach intended VLAN.
- Inter-VLAN pings fail from PC1.

**Evidence:** See `captures/troubleshooting/2025-10-13_access-port-wrong-vlan_before.txt`

---

## Root Cause
PC1 was placed into VLAN 20 instead of VLAN 10, isolating it from its proper subnet.

---

## Fix Implemented
Return Gi0/1 to VLAN 10.
```bash
SW1(config)# interface g0/1
SW1(config-if)# switchport access vlan 10
SW1(config-if)# end
```

---

## Verification (After Fix)
- `show vlan brief` shows Gi0/1 in VLAN 10.
- PC1 ↔ PC2 pings succeed via inter-VLAN routing.

**Evidence:** See `captures/troubleshooting/2025-10-13_access-port-wrong-vlan_after.txt`

---

## Key Takeaways
- Always confirm access VLAN membership with `show vlan brief`.
