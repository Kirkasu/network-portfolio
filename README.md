# 🌐 Network Engineering Portfolio

Welcome to my hands-on **Network Engineering Portfolio** — a collection of real Cisco Modeling Labs (CML) projects built to demonstrate my understanding of **CCNA-level networking concepts** through fully documented labs.

Each project is designed, configured, and verified in a lab environment, with a focus on *clarity, troubleshooting, and documentation quality*.

---

## 📘 Overview

| Category | Topics Covered | Tools Used |
|-----------|----------------|-------------|
| **Layer 2/3 Switching** | VLANs, Trunking, Inter-VLAN Routing | Cisco IOSv / IOSvL2 |
| **Routing** | Static routes, Default routes, OSPF | Cisco IOSv |
| **Addressing** | IPv4 Subnetting, Gateway configuration | Cisco IOSv / VPCS / Linux Desktop |
| **Services** | NAT/PAT, DHCP, DNS (future projects) | Cisco IOSv |
| **Troubleshooting** | Layer 1-3 verification, Capture evidence, Break/Fix | CLI + captures |

---

## 🧩 Project Index

| Project | Description | Key Technologies |
|----------|--------------|------------------|
| [**VLAN & Inter-VLAN Routing (ROAS)**](projects/vlan-intervlan-routing/README.md) | Implements VLAN segmentation and router-on-a-stick inter-VLAN routing. Includes 4 documented Break/Fix scenarios. | VLANs, 802.1Q, Trunks, Subinterfaces, Gateway, Layer-3 Routing |
| *(Coming Soon)* NAT/PAT Edge Router | Demonstrates inside/outside NAT and PAT translation verification. | NAT, PAT, ACLs |
| *(Planned)* OSPF Multi-Area Design | Multi-router topology with OSPF area separation and route summarization. | OSPFv2, LSAs, Area Border Routers |

---

## 🧱 Project Structure

Each lab follows a consistent folder layout:

```
projects/<lab-name>/
 ├── README.md                  # Lab overview and documentation
 ├── diagram/                   # Topology PNG or Draw.io export
 ├── configs/                   # Device running configs
 ├── captures/                  # Verification outputs and troubleshooting evidence
 ├── breakfix/                  # Realistic Break/Fix case studies
 └── tests/ (optional)          # Automated checks or notes
```

---

## 🧠 Break/Fix Documentation Format

Each scenario includes:
- Problem description (intentional misconfiguration)
- Commands used to break/fix
- CLI evidence (before/after)
- Root cause and verification
- Lessons learned

Example: [`trunk_not_trunk.md`](projects/vlan-intervlan-routing/breakfix/trunk_not_trunk.md)

---

## ⚙️ Tools & Environment

- **Cisco Modeling Labs (CML)** — Personal license  
- **Cisco IOSv / IOSvL2 images**  
- **VPCS / Linux Desktop nodes** for endpoint testing  
- **Visual Studio Code + PowerShell** for local repo management  
- **GitHub** for documentation and version control

---

## 🧾 How to Reproduce Labs

Each project’s README includes:
1. **Topology Diagram**
2. **IP/VLAN Table**
3. **Device Configurations**
4. **Verification Commands**
5. **Troubleshooting Scenarios**
6. **Captures Folder** with outputs

To replicate:
1. Recreate the topology in Cisco Modeling Labs or your preferred emulator.
2. Configure devices using the provided configs.
3. Follow the project README for verification and break/fix flows.

---

## 💡 Future Roadmap

- NAT/PAT and ACL project (Edge Connectivity)
- OSPF Multi-Area Design (Routing Domain)
- FHRP (HSRP/VRRP) Redundancy project
- Basic Network Automation with Ansible
- Cloud-edge hybrid connectivity demo (AWS VPC + on-prem sim)

---

## 📫 Contact

**Kirk “Kirkasu” Dickens**  
Network & Systems Engineer | CCNA | Security+ | Python  
https://www.linkedin.com/in/kirk-dickens-09b765212/


---