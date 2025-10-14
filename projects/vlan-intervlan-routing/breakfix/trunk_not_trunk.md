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
Impact: The trunk between SW1 and R1 is removed, preventing VLAN tags (802.1Q) from being passed to the router subinterfaces.
## Observed Symptoms
- No inter-VLAN connectivity
- `show interfaces trunk` shows no trunks
- `show vlan brief` lists g0/0 as access
- Router subinterfaces never receive tagged frames
- 
## Troubleshooting Steps
- Check VLAN assignment on SW1:
```bash
SW1# show vlan brief
VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Gi0/0, Gi0/2
10   Users                            active    Gi0/1
20   Guests                           active    Gi0/3
```

- Confirm trunk/access status:
```bash
SW1# show interfaces trunk
Port        Mode         Encapsulation  Status        Native vlan
Gi0/0       auto         n-802.1q       not-trunking  1
```

- Check router subinterfaces:
```bash
R1# show ip interface brief
Interface        IP-Address      OK? Method Status  Protocol
G0/0.10          192.168.10.1    YES manual up      up
G0/0.20          192.168.20.1    YES manual up      up
```

-Test connectity from PC1 to PC2:
```bash
PC1> ping 192.168.20.100
Request timed out.
Request timed out.
Request timed out.
Request timed out.
```
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
Reply from 192.168.20.100: bytes=32 time<1ms TTL=128

```
