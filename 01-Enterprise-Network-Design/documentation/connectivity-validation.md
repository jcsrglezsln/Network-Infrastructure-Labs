# Network Connectivity Validation

## 1. Objective

Validate Layer 2 connectivity, VLAN segmentation and inter-VLAN routing within the Helmet Technologies enterprise network using Cisco Packet Tracer.

## 2. Test Environment

| Component | Configuration |
|---|---|
| Simulation tool | Cisco Packet Tracer |
| Source device | PC3 |
| Source VLAN | VLAN 10 – IT |
| Source IP | 192.168.10.10 |
| Default gateway | 192.168.10.1 |
| Destination VLANs | VLAN 20, VLAN 30 and VLAN 40 |

## 3. Network Infrastructure Validated

The following components were configured and tested:

- VLAN 10 – IT
- VLAN 20 – ADMIN
- VLAN 30 – OPERATIONS
- VLAN 40 – SERVERS
- VLAN 99 – MANAGEMENT
- 802.1Q trunk links between the Core and Access switches.
- Inter-VLAN routing on HT-SW-CORE01.
- Switch Virtual Interfaces (SVIs) configured as default gateways.

## 4. IP Addressing

| VLAN | Network | Default Gateway |
|---|---|---|
| 10 – IT | 192.168.10.0/27 | 192.168.10.1 |
| 20 – ADMIN | 192.168.20.0/27 | 192.168.20.1 |
| 30 – OPERATIONS | 192.168.30.0/26 | 192.168.30.1 |
| 40 – SERVERS | 192.168.40.0/28 | 192.168.40.1 |
| 99 – MANAGEMENT | 192.168.99.0/27 | 192.168.99.1 |

## 5. Connectivity Test Results

Tests were executed from PC3 (192.168.10.10).

| Destination | Description | Initial Result |
|---|---|---|
| 192.168.10.1 | VLAN 10 default gateway | 4/4 successful |
| 192.168.30.10 | PC in VLAN 30 | 3/4 successful |
| 192.168.40.10 | HT-DC01 | 3/4 successful |
| 192.168.40.11 | HT-FS01 | 3/4 successful |
| 192.168.40.12 | HT-APP01 | 3/4 successful |

**Additional validation:** A subsequent ping test to 192.168.40.10 returned 4/4 successful packets with 0% packet loss.

## 6. Observations

- Inter-VLAN communication is operational.
- The Core multilayer switch successfully routes traffic between the configured VLANs.
- The 802.1Q trunk links carry the required VLAN traffic.
- Initial packet loss was observed on the first ping attempts. This may occur while ARP resolution takes place.
- No inter-VLAN access control lists (ACLs) have been implemented at this stage.

## 7. Conclusion

The initial connectivity tests demonstrate that the enterprise network topology, VLAN configuration, trunk links and inter-VLAN routing are operational.

Further validation is required to confirm zero packet loss on every destination.

The next implementation phase will focus on network services, including DHCP and DNS.

---

**Project:** Helmet Technologies – Network Infrastructure Labs  
**Platform:** Cisco Packet Tracer  
**Document:** Network Connectivity Validation  
**Status:** Initial validation completed
