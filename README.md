# Lab Write-Up: Router-on-a-Stick and Dynamic Trunking Protocol (DTP)

A networking lab built in [PingMyNetwork Sandbox](https://pingmynetwork.com/sandbox) to practice VLAN configuration, IEEE 802.1Q trunking, Dynamic Trunking Protocol (DTP), and inter-VLAN routing using a Cisco router and two switches.

## 1. Project Overview

This project demonstrates how devices in different VLANs can communicate through a single physical router interface using the Router-on-a-Stick method.

### Objectives

- Create and configure VLANs.
- Configure trunk links between network devices.
- Understand Dynamic Trunking Protocol (DTP).
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
- **DTP:** A Cisco-proprietary protocol that allows compatible switches to negotiate trunk links.
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
 switchport nonegotiate 
 no shutdown

interface range FastEthernet0/3-4
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 switchport nonegotiate 
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
 switchport nonegotiate 
 no shutdown

interface FastEthernet0/3
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 switchport nonegotiate 
 no shutdown

end
```

## 5. Dynamic Trunking Protocol (DTP)

DTP is a Cisco-proprietary protocol that allows compatible Cisco switches to negotiate whether a link should operate as a trunk.

### Common DTP Modes

| Mode                | Description                                                                                                 |
| ------------------- | ----------------------------------------------------------------------------------------------------------- |
| `dynamic auto`      | Passively waits for the other side to initiate trunk negotiation.                                           |
| `dynamic desirable` | Actively attempts to negotiate a trunk.                                                                     |
| `trunk`             | Manually configures the interface as a trunk. On supported platforms, DTP negotiation may still be enabled. |
| `nonegotiate`       | Disables DTP negotiation on the interface.                                                                  |

### DTP Negotiation Examples

On supported Cisco switches, the following combinations illustrate common negotiation behavior:

| Side A              | Side B              | Expected Result                    |
| ------------------- | ------------------- | ---------------------------------- |
| `dynamic desirable` | `dynamic auto`      | Trunk                              |
| `dynamic desirable` | `dynamic desirable` | Trunk                              |
| `dynamic auto`      | `dynamic auto`      | Access/non-trunk link              |
| `trunk`             | `dynamic auto`      | Trunk, if supported and compatible |

Actual behavior depends on the switch platform and software.

### Manually Configure a Trunk and Disable DTP

The following is an optional example for compatible Cisco switches:

```cisco
configure terminal
interface GigabitEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 switchport nonegotiate
 no shutdown
end
```

**Important:** Configure the other end of the link manually as a trunk as well. The `switchport nonegotiate` command is not supported on every switch or virtual image, so check platform support before using it.

### DTP Verification

```cisco
show interfaces trunk
show dtp interface GigabitEthernet0/1
```

- `show interfaces trunk` displays operational trunk links and the VLANs they carry.
- `show dtp interface GigabitEthernet0/1` displays DTP information on platforms that support the command.

**Security best practice:** Configure trunk links explicitly when appropriate, allow only the VLANs required, and disable DTP negotiation when supported and suitable for the network design.

**Lab note:** The switch configurations above use `switchport mode trunk` to configure trunks manually. The optional DTP example explains trunk negotiation and is not automatically part of the main lab configuration.

## 6. Verification and Testing

Use these Cisco IOS commands to inspect the configuration and troubleshoot connectivity.

| Command                                 | Purpose                                                |
| --------------------------------------- | ------------------------------------------------------ |
| `show ip interface brief`               | Checks router interface status and IP addresses.       |
| `show interfaces trunk`                 | Checks operational trunk links.                        |
| `show vlan brief`                       | Displays configured VLANs and access-port assignments. |
| `show ip route`                         | Displays the router's routing table.                   |
| `show dtp interface GigabitEthernet0/1` | Checks DTP information, if supported.                  |
| `ping 10.0.18.45`                       | Tests reachability to the destination host.            |

### Test Results

Record the actual results observed in [PingMyNetwork Sandbox](https://pingmynetwork.com/sandbox):

- **Router subinterfaces:** Pending verification
- **Switch trunk links:** Pending verification
- **DTP behavior:** Pending verification
- **Connectivity between VLANs:** Pending verification

A successful ping between hosts in different VLANs demonstrates inter-VLAN connectivity when the hosts, VLANs, trunk links, and default gateways are correctly configured.

The destination `10.0.18.45` belongs to the VLAN 20 subnet. A host must be configured with that address, or another reachable destination must be used, for the ping test to succeed.

## 7. Skills Demonstrated

- VLAN creation and configuration
- IEEE 802.1Q trunking
- Dynamic Trunking Protocol (DTP)
- Router subinterface configuration
- Inter-VLAN routing
- IPv4 addressing and subnetting
- Cisco IOS command-line interface (CLI)
- Network troubleshooting and connectivity testing
- Network simulation using PingMyNetwork Sandbox

## 8. Project Structure

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

## 9. Learning Resource

This lab is part of my networking practice and CCNA learning journey.

Visit [PingMyNetwork Sandbox](https://pingmynetwork.com/sandbox) to explore the lab environment and continue practicing networking skills.





