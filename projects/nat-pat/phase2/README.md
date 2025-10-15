
# NAT/PAT — Phase 2 (PAT/Overload) — Complete README

**Goal:** Many inside hosts share one public IP (R1 g0/1) via PAT.  
**Topology:** PC1 — SW1 — R1 — R2  (R2 has Lo0 = 198.51.100.10/32)

---

## IP Plan (Concrete)
| Device/Intf | Address/Mask          | Notes                 |
|-------------|------------------------|-----------------------|
| PC1         | 192.168.10.10 /24      | GW 192.168.10.1      |
| R1 g0/0     | 192.168.10.1 /24       | **ip nat inside**    |
| R1 g0/1     | 203.0.113.1 /30        | **ip nat outside**   |
| R2 g0/0     | 203.0.113.2 /30        | Upstream link        |
| R2 Lo0      | 198.51.100.10 /32      | Public test server   |

**Routing**
- R1: `ip route 0.0.0.0 0.0.0.0 203.0.113.2`
- R2: `ip route 192.168.10.0 255.255.255.0 203.0.113.1`

---

## R1 Configuration Changes (Phase 2)

```bash
conf t
interface g0/0
 description TO-SW1 (INSIDE)
 ip nat inside
!
interface g0/1
 description TO-R2 (OUTSIDE)
 ip nat outside
!
access-list 10 permit 192.168.10.0 0.0.0.255
ip nat inside source list 10 interface g0/1 overload
end
wr
```

---

## Verification — Representative Outputs


### PC1 → Public Host (R2 Lo0)

**PC1 (Windows) — `ping 198.51.100.10`**
```text
PC1:~# ping -c 4 198.51.100.10
PING 198.51.100.10 (198.51.100.10): 56 data bytes

--- 198.51.100.10 ping statistics ---
4 packets transmitted, 0 packets received, 100% packet loss
PC1:~# ping -c 4 198.51.100.10
PING 198.51.100.10 (198.51.100.10): 56 data bytes
64 bytes from 198.51.100.10: seq=0 ttl=254 time=3.362 ms
64 bytes from 198.51.100.10: seq=1 ttl=254 time=2.819 ms
64 bytes from 198.51.100.10: seq=2 ttl=254 time=3.067 ms
64 bytes from 198.51.100.10: seq=3 ttl=254 time=3.360 ms

--- 198.51.100.10 ping statistics ---
4 packets transmitted, 4 packets received, 0% packet loss
round-trip min/avg/max = 2.819/3.152/3.362 ms
```

**PC1 — `traceroute 198.51.100.10`**
```text
PC1:~# traceroute 198.51.100.10
traceroute to 198.51.100.10 (198.51.100.10), 30 hops max, 46 byte packets
 1  192.168.10.1 (192.168.10.1)  2.927 ms  2.235 ms  1.789 ms
 2  203.0.113.2 (203.0.113.2)  4.005 ms  4.094 ms  *
```

---

### R1 — NAT Translations & Stats

**R1 — `show ip nat translations`**
```text
Pro  Inside global       Inside local        Outside local       Outside global
icmp 203.0.113.1:6       192.168.10.10:6     198.51.100.10:6     198.51.100.10:6
```

**R1 — `show ip nat translations verbose` (snippet)**
```text
Pro  Inside global         Inside local          Outside local         Outside global
icmp 203.0.113.1:6         192.168.10.10:6       198.51.100.10:6       198.51.100.10:6
    create 00:00:02, use 00:00:02, timeout 00:00:58, left 00:00:58
```

**R1 — `show ip nat statistics`**
```text
Total active translations: 1 (1 static, 0 dynamic; 0 extended)
Peak translations: 4, occurred 00:00:20 ago
Outside interfaces:
  GigabitEthernet0/1
Inside interfaces:
  GigabitEthernet0/0
Hits: 24  Misses: 0
Expired translations: 3
Dynamic mappings:
-- Inside Source
[Id: 1] access-list 10 interface GigabitEthernet0/1 overload
Queued Packets: 0
```

**R1 — `show access-lists 10`**
```text
Standard IP access list 10
    10 permit 192.168.10.0, wildcard bits 0.0.0.255 (7 matches)
```

---

### Phase-1 Style Sanity Checks (still valid)

**R1 — `show ip interface brief` (snippet)**
```text
Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0     192.168.10.1    YES manual up                    up
GigabitEthernet0/1     203.0.113.1     YES manual up                    up
Loopback0              unassigned      YES unset  administratively down down
```

**R1 — `show ip route` (snippet)**
```text
Gateway of last resort is 203.0.113.2 to network 0.0.0.0

S*   0.0.0.0/0 [1/0] via 203.0.113.2
C    192.168.10.0/24 is directly connected, GigabitEthernet0/0
C    203.0.113.0/30 is directly connected, GigabitEthernet0/1
```

**R2 — `show ip route` (snippet)**
```text
C    203.0.113.0/30 is directly connected, GigabitEthernet0/0
S    192.168.10.0/24 [1/0] via 203.0.113.1
```
**R2 — `ping 203.0.113.1`**
```text
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 203.0.113.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/2/4 ms
```

---

## Break/Fix Library (Phase 2)

### BF1 — Missing `ip nat outside` on g0/1
**Break**
```bash
conf t
interface g0/1
 no ip nat outside
end
clear ip nat translations *
```

**Symptom**
- PC1 ping to 198.51.100.10 fails or is inconsistent.
- `show ip nat translations` remains empty.

**Proof (Representative)**

`show ip nat translations` (broken)
```text
Pro  Inside global       Inside local        Outside local       Outside global
<no entries>
```

**Fix**
```bash
conf t
interface g0/1
 ip nat outside
end
clear ip nat translations *
```

**After Fix — `show ip nat translations`**
```text
Pro  Inside global       Inside local        Outside local       Outside global
icmp 203.0.113.1:8       192.168.10.10:8     198.51.100.10:8     198.51.100.10:8
```

---

### BF2 — ACL too narrow (PC1 not matched)
**Break**
```bash
conf t
no access-list 10
access-list 10 permit 192.168.10.99 0.0.0.0   ! PC1 is 192.168.10.10, so no match
end
clear ip nat translations *
```

**Symptom**
- PC1 outbound pings fail (no translations created).

**Proof (Representative)**

`show access-lists 10` (broken)
```text
Standard IP access list 10
    10 permit 192.168.10.99 (0 matches)
```

`show ip nat statistics` (broken, no hits increase)
```text
Total active translations: 0 (0 static, 0 dynamic; 0 extended)
Hits: 0  Misses: 0
```

**Fix**
```bash
conf t
no access-list 10
access-list 10 permit 192.168.10.0 0.0.0.255
end
clear ip nat translations *
```

**After Fix — `show access-lists 10`**
```text
Standard IP access list 10
    10 permit 192.168.10.0, wildcard bits 0.0.0.255 (3 matches)
```

---

## Notes
- For clean evidence, clear translations before each test: `clear ip nat translations *`.
- ICMP shows as protocol `icmp` with an ID in the NAT table; TCP/UDP will show port numbers.

## Success Criteria (Checklist)
- [x] PC1 reaches `198.51.100.10`.
- [x] `show ip nat translations` on R1 displays inside local `192.168.10.10` mapped to inside global `203.0.113.1`.
- [x] `show ip nat statistics` shows non-zero hits and current active translations.
- [x] ACL 10 has non-zero matches and is subnet-wide (`192.168.10.0/24`).

---

## File Map (where to store your raw outputs)
- `projects/nat-pat/phase2/configs/R1_phase2.txt` — your exact R1 config session
- `projects/nat-pat/phase2/verification/phase2/` — pings, traces, NAT tables, stats, ACL counters
- `projects/nat-pat/phase2/verification/phase2_breakfix_1/` — BF1 before/after
- `projects/nat-pat/phase2/verification/phase2_breakfix_2/` — BF2 before/after

---

*End of README*
