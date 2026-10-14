# first-repo
router-on-a-stick
## Network Topology

![Router-on-a-Stick Network Topology](topology/router-on-a-stick-topology.png)
## Project Overview

This project demonstrates a Cisco Router-on-a-Stick configuration that enables communication between multiple VLANs using a single physical router interface.

### Objectives
- Create and configure multiple VLANs.
- Configure a trunk link between the switch and router.
- Configure router subinterfaces using IEEE 802.1Q encapsulation.
- Enable inter-VLAN routing.
- Verify connectivity between devices in different VLANs.
## VLAN Configuration

The network uses VLANs to separate devices into different logical networks.

| VLAN | Purpose |
|---|---|
| VLAN 10 | Department 1 |
| VLAN 20 | Department 2 |
| VLAN 30 | Department 3 |

Each VLAN has its own subnet. The router uses subinterfaces to route traffic between VLANs.

### Key Concepts
- **VLAN:** Separates devices into logical Layer 2 networks.
- **Trunk link:** Carries traffic from multiple VLANs over one physical link.
- **Router subinterface:** Provides routing for a specific VLAN.
- **802.1Q:** Adds VLAN tags to Ethernet frames on the trunk link.
