# NAT/PAT Project — Phase 2 Break/Fix #2 Notes

**Scope:** R1 NAT not translating traffic from PC1 to public IPs due to incorrect ACL match.  
**Status:** ✅ Fixed

---

## Cause → Symptom → Fix

**Cause**  
ACL 10 used for `ip nat inside source list 10 interface g0/0 overload` did **not** match the inside subnet (too restrictive / wrong network or wildcard), so no packets were eligible for NAT.

**Symptom**  
- `ping` from PC1 (192.168.10.0/24) to public IPs failed.  
- `show ip nat translations` on R1 showed **no dynamic entries**.  
- `show ip nat statistics` counters not increasing for inside source translations.  
- Access-list hit counts at 0 for ACL 10.

**Fix**  
Update ACL 10 to correctly match the inside subnet and confirm inside/outside interface roles.

```ios
R1# conf t
R1(config)# no access-list 10
R1(config)# access-list 10 permit 192.168.10.0 0.0.0.255
!
! (If not already set)
R1(config)# interface g0/1
R1(config-if)# ip nat inside
R1(config)# interface g0/0
R1(config-if)# ip nat outside
!
! NAT overload statement (existing or re-apply for clarity)
R1(config)# ip nat inside source list 10 interface g0/0 overload
R1(config)# end
R1# write mem
```

---

## Verification (after fix)

Run these from the appropriate nodes and capture outputs:

### On PC1
```bash
ping 203.0.113.2 -c 4   # or Windows: ping 203.0.113.2
```

### On R1
```ios
show ip nat translations
show ip nat statistics
show access-lists 10
```

**Expected:**  
- PC1 ping to 203.0.113.2 succeeds.  
- `show ip nat translations` displays dynamic entries with the inside local (192.168.10.x) mapped to the outside global (R1 g0/0 IP).  
- `show ip nat statistics` shows increasing hits/allocations.  
- ACL 10 shows non-zero hit counts.

---

## Evidence files to (re)capture

- `phase2_breakfix_2\pc1_ping_public_fixed.txt`
- `phase2_breakfix_2\r1_nat_table_fixed.txt`
- `phase2_breakfix_2\r1_nat_stats_fixed.txt`
- `phase2_breakfix_2\r1_acl10_fixed.txt`

> Tip: Use your usual capture method (Tee/Redirect) from each device/terminal so the exact command outputs land in the files above.

---

## Quick “Why it broke”

Dynamic NAT/PAT relies on two things to match flows:
1) Correct **inside/outside** interface roles.  
2) A **permit** in the referenced ACL that selects inside-local addresses for translation.  

If either is wrong, the NAT rule never triggers, so no translations are created.

---

## Rollback / Safety

To revert just the ACL change:
```ios
conf t
no access-list 10
access-list 10 permit <previous/alternate-subnet> <wildcard>
end
write mem
```

---

## Metadata

- Device: **R1**
- NAT Rule: `ip nat inside source list 10 interface g0/0 overload`
- Inside subnet: `192.168.10.0/24`
- Affected host(s): **PC1**
