# Break/Fix: Router Physical Interface Down
**Date:** 2025-10-13  
**Status:** ✅ Resolved

---

## Objective
Show how shutting down R1's physical interface breaks subinterfaces and inter-VLAN routing, and restore it.

---

## Topology Context
- R1 physical G0/0 carries 802.1Q subinterfaces .10 and .20.
- SW1 G0/0 trunk to R1 G0/0.

---

## Problem Introduced
Shut down R1's G0/0.
```bash
R1(config)# interface g0/0
R1(config-if)# shutdown
R1(config-if)# end
```

---

## Observed Symptoms (Before Fix)
- `show ip interface brief` shows G0/0 down/down and subinterfaces down.
- Pings between VLANs fail.

**Evidence:** See `captures/troubleshooting/2025-10-13_router-phy-down_before.txt`

---

## Root Cause
Subinterfaces depend on the parent physical interface being up.

---

## Fix Implemented
Bring G0/0 back up.
```bash
R1(config)# interface g0/0
R1(config-if)# no shutdown
R1(config-if)# end
```

---

## Verification (After Fix)
- `show ip interface brief` shows G0/0 up/up and subinterfaces up/up.
- Inter-VLAN pings succeed.

**Evidence:** See `captures/troubleshooting/2025-10-13_router-phy-down_after.txt`

---

## Key Takeaways
- Always check Layer 1/2 first (`show ip interface brief` is your friend).
