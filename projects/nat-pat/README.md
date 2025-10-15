# NAT/PAT Project
This multi-phase project implements and tests inside source NAT (static, pools) and PAT (overload) on an edge router, with deliberate break/fix cases.

## Phases
- **Phase 1:** Baseline L3 (no NAT) — topology, IP plan, routes, and “before” verification.
- **Phase 2:** PAT (overload) + verification and translations.
- **Phase 3:** Static NAT for a server.
- **Phase 4:** NAT pool + ACL nuances.
- **Phase 5:** Break/Fix library (common NAT misconfigs, symptoms, and fixes).
