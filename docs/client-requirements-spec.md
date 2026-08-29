# Mooifontein Game & Cattle Ranch (Vryburg)

**Client ID:** CLI-040

## 1. Executive Summary

Mooifontein Game & Cattle Ranch is a privately operated agriculture combined game reserve and cattle farming operation located near rural Vryburg, North-West Province, spanning approximately 1,200 hectares (assumed based on comparable game-and-cattle operations in the region; no official figure was provided by the client).

The organisation's operational model, including wildlife management, cattle production, veterinary services, and perimeter security, is currently supported by an integrated IP network (**OSPF single-area dynamic routing**). This results in isolated data, manual record-keeping between sites, and no centralised visibility of security or livestock tracking.

This document establishes the functional and non-functional requirements for a new multi-site IPv4 network infrastructure. It further incorporates the technical and security constraints introduced by **Change of Request (CR12)**: the planned deployment of Internet-of-Things (IoT) sensors across game-field operations and livestock management areas.

The requirements therefore serve as the authoritative baseline for the proposed physical topology, logical segmentation strategy, and IP addressing plan.

---

## 2. Client Background & Operational Context

- **Organisation type:** Private game reserve and commercial cattle ranch
- **Geographic location:** Rural, secluded area outside Vryburg, North-West Province
- **Property scale:** ~1,200 hectares (estimated based on regional comparables)
- **Core business lines:** Wildlife/game management; cattle/livestock farming
- **Support functions:** Administration & finance, veterinary services, perimeter security

**Infrastructure constraint:** Due to the property's remote location, fixed-line DSL/fibre connectivity cannot be assumed. Wide-area network (WAN) access and inter-site links must be designed around wireless and satellite/cellular technologies.

---

## 3. Operational Areas & Network Stakeholders

The ranch is organised into three physical sites, each hosting distinct operational functions with unique demands.

| Site | Operational Function | Primary Network Role | Key Systems |
|---|---|---|---|
| **Site A: Main House** | Administration, finance, payroll, records, general management | Core site; WAN egress point; security control centre | File/print servers, customer database, management workstations, NVR/security server |
| **Site B: Cattle & Livestock Complex** | Cattle tracking, feeding, weighing, veterinary records | Remote operational site | Livestock management workstations, veterinary terminals, IoT gateways (CR12) |
| **Site C: Game Reserve / Field Station** | Wildlife monitoring, ranger operations, field telemetry | Remote operational site | Ranger workstations, telemetry collection, IoT gateways (CR12), field Wi-Fi |

---

## 4. Functional Requirements

### 4.1 Inter-Site Connectivity

- **FR-01:** The network shall provide IP connectivity between Site A (Main House), Site B (Cattle Complex), and Site C (Game Reserve).
- **FR-02:** Inter-site links shall use wireless point-to-point (PtP) backhaul, given the impracticality of trenching cable across 1,200 hectares of ranchland.
- **FR-03:** The WAN uplink shall be provisioned via satellite (VSAT) or high-gain cellular LTE, as fixed-line ISP infrastructure is unavailable at the property.
- **FR-04:** Dynamic routing shall be implemented using OSPF single-area across all site routers. This ensures automatic route convergence, simplifies future site additions, and eliminates the operational overhead of static route maintenance in a remote environment where on-site IT support is limited.

### 4.2 Departmental Segmentation

- **FR-05:** Each operational function shall reside in a dedicated VLAN/subnet to contain broadcast domains and enforce access control at Layer 3.
- **FR-06:** The Security & Surveillance function requires guaranteed bandwidth for IP camera and sensor telemetry streams, plus a reliable signalling path to alert the Administration VLAN at Site A.
- **FR-07:** VLANs shall span sites only where operationally necessary; otherwise, inter-VLAN traffic must be routed through the site router to permit ACL inspection.

### 4.3 IoT / Operational Segment (CR12)

- **FR-08:** A dedicated IoT VLAN/subnet shall be provisioned for sensor devices deployed under CR12.
- **FR-09:** The IoT segment shall be logically isolated from administrative and financial systems. IoT devices must not possess Layer 3 reachability to subnets hosting confidential customer records or payroll data.
- **FR-10:** IoT gateways shall aggregate sensor data and forward it to a central collector or management platform; bi-directional initiation from the IoT subnet into corporate VLANs shall be denied by default.

---

## 5. Non-Functional Requirements & Constraints

| ID | Requirement | Rationale |
|---|---|---|
| **NFR-01 Confidentiality** | Customer records are classified as confidential. Access to systems storing these records shall be restricted to the Administration VLAN and enforced via Layer 3 ACLs and VLAN isolation. | Protects sensitive financial and personal data; satisfies basic POPIA-aligned access control. |
| **NFR-02 IoT Isolation (CR12)** | The IoT sensor segment must be isolated from all departmental traffic. No direct routing path shall exist between IoT hosts and Administration/Finance hosts. | IoT devices present elevated risk (default credentials, infrequent patching). Isolation limits lateral movement in a breach. |
| **NFR-03 Scalability** | The design shall accommodate future growth—additional sensors, new operational sites, or new departments—without requiring a full network redesign. | Justifies OSPF over static routing and VLSM over fixed-size subnetting. |
| **NFR-04 Resilience** | Critical infrastructure (security cameras, gate controllers, WAN routers) shall be supported by uninterruptible power supplies (UPS) or solar backup, given unreliable rural power. | Ensures security perimeter and WAN availability during outages. |
| **NFR-05 Addressing** | All IPv4 addressing shall be drawn from the assigned block **10.22.0.0/16** and subdivided using **VLSM** to match the relative host density of each segment. | Prevents address waste; allows granular growth per VLAN. |

---

## 6. Security & Access Control Requirements

- **SR-01:** Inter-VLAN routing shall be performed at the site router or Layer 3 switch, where stateless ACLs shall filter traffic between operational segments.
- **SR-02:** The Administration VLAN shall be designated a **high-trust zone**. Only explicitly permitted traffic may enter this subnet; all other sources shall be denied by default.
- **SR-03:** IoT devices shall reside in a **low-trust zone**. Their gateway shall permit outbound connections to designated collector IPs only; inbound connections to IoT hosts shall be dropped unless part of an established management session initiated from Site A.
- **SR-04:** Management access to routers and switches (SSH/SNMP) shall be restricted to the Administration VLAN at Site A.

---

## 7. Assumptions & Dependencies

Where the client brief does not specify detail, the following assumptions apply:

| ID | Assumption |
|---|---|
| **A-01** | Ranch size is approximately 1,200 hectares, derived from regional operational comparables. |
| **A-02** | WAN connectivity is satellite (VSAT) or cellular LTE; no fixed-line ISP access is available. |
| **A-03** | Inter-site links are wireless point-to-point due to terrain and distance constraints. |
| **A-04** | CR12 IoT deployment applies primarily to Site B (livestock) and Site C (game/field operations). |
| **A-05** | Each site is equipped with at least one OSPF-capable router and one managed switch to support VLAN trunking. |
| **A-06** | Network equipment will be centrally managed via SSH from Site A. |

---

## 8. Design Traceability Summary

| Requirement | Topology Impact | Addressing Impact |
|---|---|---|
| **FR-01, FR-02, FR-04** | Three-site physical topology with PtP wireless links; OSPF neighbour adjacencies between site routers. | `/30` or `/31` subnets for PtP inter-router links. |
| **FR-05, FR-06** | VLAN-per-department logical topology; trunk links between switches and routers. | Dedicated subnet per VLAN using VLSM from `10.22.0.0/16`. |
| **FR-08, FR-09, NFR-02** | Dedicated IoT access-layer switch ports or SSIDs; firewall/ACL placement at router. | Isolated subnet for IoT; no route redistribution into Admin VLAN. |
| **NFR-05** | N/A | VLSM table documenting network, broadcast, usable range, and gateway per subnet. |
| **NFR-04** | UPS/solar symbols on physical topology at remote sites. | N/A |
