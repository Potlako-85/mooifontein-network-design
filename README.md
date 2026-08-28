# Mooifontein Ranch Network Infrastructure Design

 A multi-site IPv4 network design for a 1,200-hectare game reserve and cattle ranch in rural North-West Province, South Africa.

## Overview

Mooifontein Game & Cattle Ranch operates across three geographically dispersed sites with no existing integrated data network. This project delivers a complete network infrastructure design—including requirements specification, physical and logical topology, and a VLSM addressing plan—to connect the Main House, Cattle & Livestock Complex, and Game Reserve Field Station.

The design accommodates a recent **Change Request (CR12)** for IoT sensor deployment while maintaining strict isolation between operational technology and administrative systems.

## Project Scope

| Deliverable | Status |
|---|---|
| Client Requirements Specification | Complete |
| Physical Topology Design | Complete |
| Logical Topology Design | Complete |
| IP Addressing Plan (VLSM) | Complete |
| Packet Tracer Simulation | In Progress |
| Implementation & Configuration | ⏳ Pending |

## Design Highlights

- **3-site hub-and-spoke topology** with wireless point-to-point backhaul
- **OSPF single-area (Area 0)** dynamic routing across all site routers
- **VLAN segmentation** per department (Admin, Security, Livestock, Veterinary, Game Ops, Rangers, IoT)
- **VLSM addressing** from `10.22.0.0/16` with efficient subnet allocation
- **IoT isolation (CR12)** via dedicated VLANs and router ACLs—IoT segments cannot reach administrative subnets
- **Resilience planning** with UPS/solar backup noted for remote sites

## Technologies & Concepts

- OSPFv2 (Single-Area)
- VLANs & Inter-VLAN Routing
- VLSM Subnetting
- Extended ACLs
- Wireless Point-to-Point Backhaul (design assumption)
- VSAT / LTE WAN (design assumption)

## Repository Structure

```text
├── docs/
│   ├── 01-requirements-specification.md
│   ├── 02-physical-topology.md
│   ├── 03-logical-topology.md
│   └── 04-ip-addressing-plan.md
├── diagrams/
│   ├── physical-topology.png
│   ├── logical-topology.png
│   └── vlsm-table.png
└── simulation/
    └── mooifontein-ranch.pkt   (Packet Tracer)


## Addressing at a Glance

| VLAN | Function | Network | Mask |
|---|---|---|---|
| 10 | Administration | 10.22.10.0 | /26 |
| 20 | Security & Surveillance | 10.22.20.0 | /25 |
| 30 | Livestock Management | 10.22.30.0 | /26 |
| 40 | Veterinary | 10.22.40.0 | /27 |
| **50** | **IoT Sensors (CR12)** | **10.22.50.0** | **/24** |
| 60 | Game / Field Ops | 10.22.60.0 | /26 |
| 70 | Ranger Operations | 10.22.70.0 | /27 |
| **80** | **IoT Sensors (CR12)** | **10.22.80.0** | **/24** |
| 99 | Network Management | 10.22.99.0 | /28 |

*PtP links: 10.22.100.0/30, 10.22.100.4/30, 10.22.100.8/30*

---

**Note:** This repository documents the design phase of the project. Configuration files and simulation exports will be added as the implementation phase progresses.
