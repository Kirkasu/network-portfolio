# Break/Fix: Trunk Port Left as Access
**Date:** 2025-10-13
**Status:** ✅ Resolved
## Objective
Show how misconfiguring the SW1→R1 link as an access port breaks inter-VLAN routing.
## Problem Introduced
Configured the SW1 uplink as access:
```bash
SW1(config)# interface g0/0
SW1(config-if)# switchport mode access
```
## Observed Symptoms
- No inter-VLAN connectivity
- `show interfaces trunk` shows no trunks
## Fix Implemented
```bash
SW1(config)# interface g0/0
SW1(config-if)# switchport mode trunk
SW1(config-if)# switchport trunk allowed vlan 10,20
```
## Verification
```bash
SW1# show interfaces trunk
Port Vlans allowed on trunk
Gi0/0 10,20
PC1> ping 192.168.20.100
```
