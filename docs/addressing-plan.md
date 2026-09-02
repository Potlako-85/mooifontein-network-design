## IP Addressing Plan (VLSM)

The entire design is carved from the assigned block **10.22.0.0/16** (65,536 addresses). VLSM is used to allocate exactly the number of addresses each segment needs, minimising waste and preserving contiguous space for future growth.

### Addressing Table

| VLAN | Site / Function | Network | Mask | Usable Range | Gateway | Hosts | Classification |
|---|---|---|---|---|---|---:|---|
| 10 | Site A — Administration | `10.22.10.0` | `/26` | `.1 – .62` | `.1` | 62 | High Trust |
| 20 | Site A — Security & Surveillance | `10.22.20.0` | `/25` | `.1 – .126` | `.1` | 126 | Camera/Sensor |
| 30 | Site B — Livestock Management | `10.22.30.0` | `/26` | `.1 – .62` | `.1` | 62 | Operations |
| 40 | Site B — Veterinary | `10.22.40.0` | `/27` | `.1 – .30` | `.1` | 30 | Operations |
| **50** | **Site B — IoT Sensors (CR12)** | **`10.22.50.0`** | **`/24`** | **`.1 – .254`** | **`.1`** | **254** | **Isolated** |
| 60 | Site C — Game / Field Ops | `10.22.60.0` | `/26` | `.1 – .62` | `.1` | 62 | Operations |
| 70 | Site C — Ranger Operations | `10.22.70.0` | `/27` | `.1 – .30` | `.1` | 30 | Operations |
| **80** | **Site C — IoT Sensors (CR12)** | **`10.22.80.0`** | **`/24`** | **`.1 – .254`** | **`.1`** | **254** | **Isolated** |
| 99 | Network Management (All Sites) | `10.22.99.0` | `/28` | `.1 – .14` | `.1` | 14 | Infrastructure |
| — | PtP: Site A ↔ B | `10.22.100.0` | `/30` | `.1 – .2` | — | 2 | Infrastructure |
| — | PtP: Site A ↔ C | `10.22.100.4` | `/30` | `.5 – .6` | — | 2 | Infrastructure |
| — | PtP: Site B ↔ C (Backup) | `10.22.100.8` | `/30` | `.9 – .10` | — | 2 | Infrastructure |

---

### Reserved & Future Space

| Range | Purpose |
|---|---|
| `10.22.0.0 – 10.22.9.255` | Reserved for future Site A expansion |
| `10.22.101.0 – 10.22.127.255` | Reserved for future Site B/C expansion |
| `10.22.128.0 – 10.22.255.255` | Reserved for new sites, guest networks, or WAN services |
| `10.22.255.0 – 10.22.255.255` | Loopback addresses for router IDs |

---

### VLSM Justification

1. **IoT subnets (`/24`):** CR12 anticipates dense sensor deployment. A `/24` provides 254 addresses per site without requiring redesign if sensor count grows.

2. **Security (`/25`):** IP cameras consume one address each. A `/25` supports up to 126 cameras—generous for perimeter and gate coverage.

3. **Administration (`/26`):** Limited to staff workstations and servers. Kept smaller than Security because headcount is low.

4. **PtP links (`/30`):** Exactly two usable addresses per link (one per router interface). Most efficient mask for point-to-point infrastructure.

5. **Contiguous infrastructure block (`10.22.100.0/24`):** All inter-site links live in one `/24`, making route summarisation trivial if the design ever moves to multi-area OSPF.
