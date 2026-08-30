# Physical Topology

## Overview

The physical topology adopts a **hub-and-spoke model** with Site A (Main House) serving as the network core. This is dictated by two hard constraints from the client context:

1. The VSAT/LTE WAN terminal can only be installed at the Main House due to power and shelter availability.
2. A full mesh of wireless PtP links across 1,200 hectares would be cost-prohibitive and spectrum-inefficient.

Site B and Site C connect back to Site A via directional **5 GHz wireless bridges**. An optional backup link between Site B and Site C provides path redundancy if the Site A–Site C link fails.

---

## Site A — Main House (Network Core)

| Component | Role | Justification |
|---|---|---|
| **Router RTR-A** | Core router, OSPF DR/BDR candidate, WAN gateway | Terminates VSAT/LTE, runs OSPF Area 0, performs inter-VLAN routing |
| **Switch SW-A** | Layer 2 access + trunk to RTR-A | Supports VLANs 10, 20, 99; PoE ports for cameras and access points |
| **VSAT Terminal / LTE Modem** | WAN egress | Only site with reliable shelter and power for CPE equipment |
| **UPS (2–4 hour runtime)** | Power resilience | Ensures security NVR and WAN remain online during outages |
| **NVR / Camera Server** | Security footage aggregation | Local storage reduces WAN bandwidth; alerts pushed to admin |
| **Admin Workstations** | Finance, payroll, records | Connected to VLAN 10 ports |

---

## Site B — Cattle & Livestock Complex

| Component | Role | Justification |
|---|---|---|
| **Router RTR-B** | Remote site router, OSPF neighbour to RTR-A | Advertises local subnets into Area 0; applies IoT ACLs |
| **Switch SW-B** | Layer 2 access | Supports VLANs 30, 40, 50; PoE for IoT gateways |
| **IoT Gateway** | Sensor data aggregation (CR12) | Collects LoRa/Zigbee sensor data and forwards via IP |
| **UPS + Solar Backup** | Resilience | Rural power outages are common; solar extends runtime indefinitely |
| **Livestock/Vet Terminals** | Operational workstations | Connected to VLAN 30 and VLAN 40 ports |

---

## Site C — Game Reserve / Field Station

| Component | Role | Justification |
|---|---|---|
| **Router RTR-C** | Remote site router, OSPF neighbour to RTR-A | Advertises local subnets into Area 0; applies IoT ACLs |
| **Switch SW-C** | Layer 2 access | Supports VLANs 60, 70, 80; outdoor-rated for field conditions |
| **IoT Gateway** | Wildlife tracking aggregation (CR12) | Collects ranger and environmental sensor data |
| **UPS + Solar Backup** | Resilience | Remote site with unreliable grid power |
| **Ranger Workstations** | Field operations | Connected to VLAN 60 and VLAN 70 ports |

---

## Inter-Site Connectivity

| Link | Technology | Distance (Est.) | Subnet | Purpose |
|---|---|---:|---|---|
| **Site A ↔ Site B** | 5 GHz Wireless PtP (e.g., Ubiquiti airFiber 5X) | ~3 km | `10.22.100.0/30` | Primary backhaul |
| **Site A ↔ Site C** | 5 GHz Wireless PtP (e.g., Ubiquiti airFiber 5X) | ~4 km | `10.22.100.4/30` | Primary backhaul |
| **Site B ↔ Site C** | 5 GHz Wireless PtP (optional backup) | ~5 km | `10.22.100.8/30` | Path redundancy |

### Reason for PtP

Trenching fibre across a 1,200-hectare working ranch is economically and environmentally impractical. Directional 5 GHz bridges offer **100+ Mbps** at these distances with line-of-sight, sufficient for camera streams, IoT telemetry, and administrative traffic.

The optional Site B–Site C link ensures that if the Site A–Site C link fails, traffic from Site C can still reach Site A via Site B. **OSPF will reconverge automatically and select the available path.**
