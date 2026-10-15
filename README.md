# first-repo
router-on-a-stick
# Router-on-a-Stick Lab

A networking lab built in [PingMyNetwork Sandbox](https://pingmynetwork.com/sandbox)  to practice VLAN configuration, IEEE 802.1Q trunking, and inter-VLAN routing using a Cisco router and two switches.

## 1. Project Overview

This project demonstrates how devices in different VLANs can communicate through a single physical router interface using the Router-on-a-Stick method.

### Objectives

- Create and configure VLANs.
- Configure trunk links between network devices.
- Configure router subinterfaces using IEEE 802.1Q encapsulation.
- Enable inter-VLAN routing.
- Verify network connectivity.
- Practice Cisco IOS commands and troubleshooting.

## 2. Network Topology
![Router-on-a-Stick Network Topology](topology/router-on-a-stick-topology.png)

The lab contains the following network devices and logical networks:

- **R1:** Routes traffic between VLANs using router subinterfaces.
- **SW1:** Connects to the router and forwards VLAN traffic.
- **SW2:** Extends Layer 2 connectivity to other devices.
- **VLAN 10 — STAFF:** Uses the `10.0.18.0/27` subnet.
- **VLAN 20 — ADMIN:** Uses the `10.0.18.32/27` subnet.



## 3. IP Addressing and VLAN Configuration

| VLAN | Name  | Network         | Default Gateway |
| ---- | ----- | --------------- | --------------- |
| 10   | STAFF | `10.0.18.0/27`  | `10.0.18.1`     |
| 20   | ADMIN | `10.0.18.32/27` | `10.0.18.33`    |

Each VLAN belongs to a separate IP subnet. The router provides the default gateway for each VLAN.

### Key Concepts

- **VLAN:** Separates devices into logical Layer 2 networks.
- **Trunk link:** Carries traffic for multiple VLANs over one link.
- **Router subinterface:** Provides a logical interface for a specific VLAN.
- **IEEE 802.1Q:** Adds VLAN tags to Ethernet frames on trunk links.
- **Inter-VLAN routing:** Allows devices in different IP subnets to communicate through a router.

## 4. Key Configurations

### Router Configuration — R1

The router uses subinterfaces on `GigabitEthernet0/1` to route traffic between VLAN 10 and VLAN 20.

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

end
```

### Switch 1 — SW1

Create VLANs 10 and 20, then configure the trunk interfaces used in the lab.

```cisco
enable
configure terminal

vlan 10
 name STAFF-10

vlan 20
 name ADMIN-20

interface GigabitEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 no shutdown

interface range FastEthernet0/3-4
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 no shutdown

end
```

### Switch 2 — SW2

Create the same VLANs and configure the trunk links.

```cisco
enable
configure terminal

vlan 10
 name STAFF-10

vlan 20
 name ADMIN-20

interface FastEthernet0/2
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 no shutdown

interface FastEthernet0/3
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 no shutdown

end
```

**Note:** These configurations reflect the intended lab setup. Confirm the interface names and trunk links against your actual [PingMyNetwork Sandbox](https://pingmynetwork.com/sandbox) topology before presenting them as verified configurations.

The complete configuration files are available in the `configs/` folder.

## 5. Verification and Testing

The following Cisco IOS commands can be used to inspect the configuration and troubleshoot connectivity.

| Command                   | Purpose                                               |
| ------------------------- | ----------------------------------------------------- |
| `show ip interface brief` | Checks router interface status and IP addresses       |
| `show interfaces trunk`   | Checks operational trunk links                        |
| `show vlan brief`         | Displays configured VLANs and access-port assignments |
| `show ip route`           | Displays the router's routing table                   |
| `ping 10.0.18.45`         | Tests reachability to the destination host            |

### Test Results

Record the actual results observed in [PingMyNetwork Sandbox](https://pingmynetwork.com/sandbox):

- **Router subinterfaces:** Pending verification
- **Switch trunk links:** Pending verification
- **Connectivity between VLANs:** Pending verification

A successful ping between hosts in different VLANs demonstrates inter-VLAN connectivity when the hosts, VLANs, trunk links, and default gateways are correctly configured.

The destination `10.0.18.45` belongs to the VLAN 20 subnet. A host must be configured with that address, or another reachable destination must be used, for the ping test to succeed.

## 6. Skills Demonstrated

- VLAN creation and configuration
- IEEE 802.1Q trunking
- Router subinterface configuration
- Inter-VLAN routing
- IPv4 addressing and subnetting
- Cisco IOS command-line interface (CLI)
- Network troubleshooting and connectivity testing
- [PingMyNetwork Sandbox](https://pingmynetwork.com/sandbox) network simulation

## 7. Project Structure

```text
router-on-a-stick/
├── README.md
├── topology/
│   └── router-on-a-stick-topology.png
└── configs/
    ├── router-config.txt
    ├── sw1-config.txt
    └── sw2-config.txt
```

## 8. Learning Resource

This lab is part of my networking practice and CCNA learning journey.

Visit [PingMyNetwork Sandbox](https://pingmynetwork.com/sandbox) to explore the lab environment and continue practicing networking skills.




