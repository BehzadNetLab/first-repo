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
| VLAN 10 | STAFF |
| VLAN 20 | ADMIN |

Each VLAN has its own subnet. The router uses subinterfaces to route traffic between VLANs.

### Key Concepts
- **VLAN:** Separates devices into logical Layer 2 networks.
- **Trunk link:** Carries traffic from multiple VLANs over one physical link.
- **Router subinterface:** Provides routing for a specific VLAN.
- **802.1Q:** Adds VLAN tags to Ethernet frames on the trunk link.
## Configuration

### 1. Switch Configuration

SW1:

Create the VLANs:

```cisco
enable
configure terminal
vlan 10
 name STAFF-10
vlan 20
 name ADMIN-20
```

Configure the trunk port connected to the router&#x20;

```cisco
interface GigabitEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 no shutdown
interface range f0/3-4
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 no shutdown

SW2:

Create the VLANs:

enable
configure terminal
vlan 10
 name STAFF-10
vlan 20
 name ADMIN-20

interface FastEthernet0/2
 switchport mode trunk

interface FastEthernet0/3
 switchport mode trunk

 
```

### 2. Router Configuration

Configure one router subinterface for each VLAN (replace `GigabitEthernet0/0` with your actual router interface):

```cisco
enable
configure terminal
interface GigabitEthernet0/1
 no shutdown

interface GigabitEthernet0/1.10
 encapsulation dot1Q 10
 ip address 10.0.18.1 255.255.255.224

interface GigabitEthernet0/1.20
 encapsulation dot1Q 20
 ip address 10.0.18.33 255.255.255.224


```

### 3. Verification

Use these commands to verify the configuration:

```cisco
show ip interface brief
show interfaces trunk
show vlan brief
show ip route
ping 10.0.18.45
```
## Testing and Verification

The following commands can be used to verify the Router-on-a-Stick configuration.

| Command | Purpose |
|---|---|
| `show ip interface brief` | Checks router interface status and IP addresses |
| `show interfaces trunk` | Verifies trunk links on the switches |
| `show vlan brief` | Displays VLANs and access-port assignments |
| `show ip route` | Checks the router's routing table |
| `ping 10.0.18.45` | Tests connectivity to the destination IP address |

### Expected Results

- VLAN 10 and VLAN 20 are configured on the switches.
- Trunk links carry traffic for the required VLANs.
- Router subinterfaces provide the default gateways for both VLANs.
- Devices in different VLANs can communicate when the IP addressing and configurations are correct.

## Skills Demonstrated

- VLAN creation and configuration
- IEEE 802.1Q trunking
- Router subinterface configuration
- Inter-VLAN routing
- IPv4 addressing and subnetting
- Cisco IOS command-line interface (CLI)
- Network troubleshooting and connectivity testing

