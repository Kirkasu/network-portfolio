# Break/Fix #1 – Missing `ip nat outside` on R1 (Phase 2)

**Date:** 2025-10-15  
**Status:** ✅ Resolved  

---

## 🧩 Objective
Demonstrate troubleshooting and resolution of a NAT/PAT failure caused by a missing `ip nat outside` configuration on R1’s external interface during Phase 2 of the NAT/PAT project.

This break/fix validates understanding of interface roles and NAT translation clearing in dynamic overload configurations.

---

## ⚙️ Network Context
- **Router:** R1  
- **Inside Interface:** G0/0 → 192.168.10.0/24  
- **Outside Interface:** G0/1 → 203.0.113.0/30  
- **NAT Type:** Dynamic PAT (overload)  

---

## ⚠️ Problem Introduced
During Phase 2 testing, the `ip nat outside` statement on R1’s `G0/1` interface was accidentally removed.

### Resulting Symptoms
- PC1 could still ping public IPs due to old NAT entries.  
- New NAT translations were not being created.  
- `show ip nat translations` output remained static or empty.  
- Connectivity tests appeared misleading (pings “working” but not truly translating).

---

## 🔬 Verification Before Fix

### PC1 Ping Test
```bash
PC1> ping 198.51.100.10
Success rate is 100 percent (5/5)
```

### R1 NAT Table
```bash
R1# show ip nat translations
Pro  Inside global      Inside local       Outside local      Outside global
icmp 203.0.113.2:3      192.168.10.10:3    198.51.100.10:3    198.51.100.10:3
```

> 🧠 *Observation:* Despite `ip nat outside` missing, the old translation persisted, giving a false impression of functionality.

---

## 🛠️ Fix (on R1)
```bash
conf t
interface g0/1
 ip nat outside
end
clear ip nat translations *
```

---

## 🔎 Verification After Fix

### Clear NAT Table
```bash
R1# clear ip nat translations *
R1# show ip nat translations
(no entries)
```

### Initiate New Traffic
```bash
PC1> ping 198.51.100.10
Success rate is 100 percent (5/5)
```

### Confirm New NAT Entries
```bash
R1# show ip nat translations
Pro  Inside global      Inside local       Outside local      Outside global
icmp 203.0.113.2:10     192.168.10.10:10   198.51.100.10:10   198.51.100.10:10
```

> ✅ *Translation table now dynamically updates as expected.*

---

## 📘 Lessons Learned
- Always verify **inside/outside designations** before NAT/PAT testing.  
- Stale translations can create misleading connectivity results.  
- Use `clear ip nat translations *` before each test iteration for accurate results.  
- Keep annotated comments in router configs to flag key NAT roles for future troubleshooting.

---

## 📄 References
- Cisco IOS NAT Configuration Guide  
- CCNA 200-301 Objective 4.5: *Configure and verify NAT and PAT*  
- Project: **NAT/PAT Phase 2 – Dynamic PAT Overload Testing**

---

**Author:** Kirk Dickens  
**Project:** `network-portfolio/projects/nat-pat/phase2/`  
**Break/Fix ID:** `phase2_breakfix_1`
