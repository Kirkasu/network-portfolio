# Phase 1 — Baseline (No NAT)
Goal: Prove clean L3 connectivity end-to-end before enabling NAT.

## Topology
PC1 — SW1 — R1(g0/0 inside, g0/1 outside) — R2(“ISP”) + Lo0=198.51.100.10/32

## IP Plan (fill your host octet for PC1)
| Device/Intf | Address/Mask        | Role                          |
|-------------|---------------------|-------------------------------|
| PC1         | 192.168.10.X /24    | Inside host                   |
| R1 g0/0     | 192.168.10.1 /24    | Default GW for PC1            |
| R1 g0/1     | 203.0.113.1 /30     | Outside toward R2             |
| R2 g0/0     | 203.0.113.2 /30     | Upstream link                 |
| R2 Lo0      | 198.51.100.10 /32   | Public test server (target)   |

## Routing
- R1: `ip route 0.0.0.0 0.0.0.0 203.0.113.2`
- R2: `ip route 192.168.10.0 255.255.255.0 203.0.113.1`

## Verification Artifacts
Raw outputs (pings, routes, ARP, interface states) live in `verification/phase1/`. No NAT yet.
