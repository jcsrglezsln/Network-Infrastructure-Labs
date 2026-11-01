## DHCP Configuration and Validation

### DHCP Server

- Server: HT-DC01
- IP address: 192.168.40.10
- DHCP service: Enabled

### DHCP Pools

| Pool | Gateway | DNS | Subnet Mask |
|---|---|---|---|
| VLAN10_IT | 192.168.10.1 | 192.168.40.10 | 255.255.255.224 |
| VLAN20_ADMIN | 192.168.20.1 | 192.168.40.10 | 255.255.255.224 |
| VLAN30_OPERATIONS | 192.168.30.1 | 192.168.40.10 | 255.255.255.192 |

### DHCP Relay Configuration

DHCP relay was configured on the multilayer switch HT-SW-CORE01:

- VLAN 10: 192.168.40.10
- VLAN 20: 192.168.40.10
- VLAN 30: 192.168.40.10

### DHCP Validation

Dynamic address assignment was tested on client devices in VLANs 10, 20 and 30.

Confirmed assignments:

| Device | VLAN | Assigned IP |
|---|---|---|
| PC3 | 10 – IT | 192.168.10.12 |
| PC5 | 20 – ADMIN | 192.168.20.10 |
| PC4 | 20 – ADMIN | 192.168.20.11 |

PC1 and PC2 also obtained addresses dynamically in VLAN 30. Their exact assigned addresses were not recorded.

## DNS Configuration and Validation

### DNS Server

- Server: HT-DC01
- IP address: 192.168.40.10
- DNS service: Enabled

### DNS Resource Records

| Hostname | Record Type | IP Address |
|---|---|---|
| ht-dc01.corp.tacos.test | A | 192.168.40.10 |
| ht-fs01.corp.tacos.test | A | 192.168.40.11 |
| ht-app01.corp.tacos.test | A | 192.168.40.12 |

### DNS Validation

DNS resolution was tested from PC5 in VLAN 20.

All three hostnames resolved successfully and returned four ICMP replies each.

- Packet loss: 0%
- DNS resolution: Successful
- Cross-VLAN communication: Successful

## Conclusion

DHCP and DNS services were successfully configured and tested in the Packet Tracer environment.

The network supports centralized DHCP address assignment for user VLANs and DNS name resolution for the three server devices.

**Implementation status:** Initial DHCP and DNS validation completed.

**Note:** These services are simulated using Packet Tracer Server-PT. They do not represent a real Windows Server DHCP or Active Directory-integrated DNS deployment.