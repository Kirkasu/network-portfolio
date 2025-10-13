# Break/Fix: Native VLAN Mismatch on Trunk
**Date:** 2025-10-13  
**Status:** ✅ Resolved

---

## Objective
Demonstrate how mismatched native VLANs on the R1↔SW1 trunk cause instability and warnings, and restore alignment.

---

## Topology Context
- R1 Router-on-a-Stick (G0/0.10 → 192.168.10.1, G0/0.20 → 192.168.20.1)  
- SW1 uplink G0/0 to R1 G0/0 should be trunk 802.1Q, native VLAN **1** by default.

---

## Problem Introduced
Set the SW1 trunk native VLAN to **999** while R1 remains on default **1**.
```bash
SW1(config)# interface g0/0
SW1(config-if)# switchport trunk native vlan 999
SW1(config-if)# end
```

---

## Observed Symptoms (Before Fix)
- Console warns of native VLAN mismatch.
- Odd behavior (e.g., ARP/ICMP irregularities) may occur.

**Evidence:** See `captures/troubleshooting/2025-10-13_native-vlan-mismatch_before.txt`

---

## Root Cause
Trunk sides disagree on which VLAN is untagged (native). Untagged frames are placed into different VLANs across the link.

---

## Fix Implemented
Align native VLAN to **1** on SW1.
```bash
SW1(config)# interface g0/0
SW1(config-if)# switchport trunk native vlan 1
SW1(config-if)# end
```

---

## Verification (After Fix)
- Trunk shows native VLAN as **1**.
- Inter-VLAN traffic stable.

**Evidence:** See `captures/troubleshooting/2025-10-13_native-vlan-mismatch_after.txt`

---

## Key Takeaways
- Always check `show interfaces trunk` for Native VLAN.
- Mismatched native VLANs produce warnings and intermittent L2 issues.
