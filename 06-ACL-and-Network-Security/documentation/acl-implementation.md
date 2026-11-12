# Stage 6 – ACL Implementation and Network Security

## 1. Objective

Implement Extended Access Control Lists (ACLs) on the multilayer switch to enforce network segmentation and control communication between VLANs.

The selected security policy is **Policy B – Web Access**.

The objective is to:
- Allow DNS queries to the internal DNS server.
- Allow HTTP and HTTPS access to the internal application server.
- Block direct communication between user VLANs.
- Restrict direct access to the server and management VLANs.
- Preserve DHCP relay traffic.
- Allow other traffic not explicitly denied by the ACL rules.

## 2. Network Environment

| Component | Configuration |
|---|---|
| Core switch | HT-SW-CORE01 |
| VLAN 10 | IT – 192.168.10.0/27 |
| VLAN 20 | ADMIN – 192.168.20.0/27 |
| VLAN 30 | OPERATIONS – 192.168.30.0/26 |
| VLAN 40 | SERVERS – 192.168.40.0/28 |
| VLAN 99 | MANAGEMENT – 192.168.99.0/27 |
| DNS server | HT-DC01 – 192.168.40.10 |
| Application server | HT-APP01 – 192.168.40.12 |

## 3. Security Policy

The ACLs are configured inbound on the corresponding VLAN interfaces (SVIs).

| Source VLAN | Allowed services | Restricted destinations |
|---|---|---|
| VLAN 20 – ADMIN | DNS, HTTP, HTTPS | VLAN 10, 30, 40 and 99 |
| VLAN 30 – OPERATIONS | DNS, HTTP, HTTPS | VLAN 10, 20, 40 and 99 |

DHCP relay traffic is also explicitly permitted.

## 4. ACL Configuration

### 4.1 VLAN 20 – ADMIN

ACL name: `ACL_VLAN20_IN`

```cisco
ip access-list extended ACL_VLAN20_IN
 permit udp any eq bootpc any eq bootps
 permit udp 192.168.20.0 0.0.0.31 host 192.168.40.10 eq domain
 permit tcp 192.168.20.0 0.0.0.31 host 192.168.40.10 eq domain
 permit tcp 192.168.20.0 0.0.0.31 host 192.168.40.12 eq www
 permit tcp 192.168.20.0 0.0.0.31 host 192.168.40.12 eq 443
 permit icmp 192.168.20.0 0.0.0.31 192.168.10.0 0.0.0.31 echo-reply
 permit tcp 192.168.20.0 0.0.0.31 192.168.10.0 0.0.0.31 established
 deny ip 192.168.20.0 0.0.0.31 192.168.10.0 0.0.0.31
 deny ip 192.168.20.0 0.0.0.31 192.168.30.0 0.0.0.63
 deny ip 192.168.20.0 0.0.0.31 192.168.99.0 0.0.0.31
 deny ip 192.168.20.0 0.0.0.31 192.168.40.0 0.0.0.15
 permit ip 192.168.20.0 0.0.0.31 any
```

Applied to the SVI:

```cisco
interface vlan 20
 ip access-group ACL_VLAN20_IN in
```

### 4.2 VLAN 30 – OPERATIONS

ACL name: `ACL_VLAN30_IN`

```cisco
ip access-list extended ACL_VLAN30_IN
 permit icmp 192.168.30.0 0.0.0.63 192.168.10.0 0.0.0.31 echo-reply
 permit udp any eq bootpc any eq bootps
 permit udp 192.168.30.0 0.0.0.63 host 192.168.40.10 eq domain
 permit tcp 192.168.30.0 0.0.0.63 host 192.168.40.10 eq domain
 permit tcp 192.168.30.0 0.0.0.63 host 192.168.40.12 eq www
 permit tcp 192.168.30.0 0.0.0.63 host 192.168.40.12 eq 443
 permit tcp 192.168.30.0 0.0.0.63 192.168.10.0 0.0.0.31 established
 deny ip 192.168.30.0 0.0.0.63 192.168.10.0 0.0.0.31
 deny ip 192.168.30.0 0.0.0.63 192.168.20.0 0.0.0.31
 deny ip 192.168.30.0 0.0.0.63 192.168.99.0 0.0.0.31
 deny ip 192.168.30.0 0.0.0.63 192.168.40.0 0.0.0.15
 permit ip 192.168.30.0 0.0.0.63 any
```

Applied to the SVI:

```cisco
interface vlan 30
 ip access-group ACL_VLAN30_IN in
```


### 4.3 VLAN 10 – IT

ACL name: `ACL_VLAN10_IN`

```cisco
ip access-list extended ACL_VLAN10_IN
 permit udp any eq bootpc any eq bootps
 permit ip 192.168.10.0 0.0.0.31 any
```

Applied to the SVI:

```cisco
interface vlan 10
 ip access-group ACL_VLAN10_IN in
```

VLAN 10 is the IT network and has unrestricted access to the other internal VLANs, including ADMIN, OPERATIONS, SERVERS and MANAGEMENT, according to the implemented policy.

## 5. Validation Results

### 5.1 VLAN 20 – ADMIN

Tests performed:
- Gateway connectivity: successful.
- Communication with VLAN 10: blocked.
- Communication with VLAN 30: blocked.
- Direct ICMP access to server VLAN: blocked.
- DNS resolution of the application server: successful.
- HTTP access to HT-APP01: successful.

### 5.2 VLAN 30 – OPERATIONS

Tests performed:
- Gateway connectivity: successful.
- Communication with VLAN 10: blocked.
- Communication with VLAN 20: blocked.
- Direct ICMP access to server VLAN: blocked.
- DNS resolution of the application server: successful.
- HTTP access to HT-APP01: successful.

### 5.3 ACL Counters

The `show access-lists` command confirmed matching traffic on both ACLs.

| ACL rule | VLAN 20 matches | VLAN 30 matches |
|---|---:|---:|
| DNS UDP | 2 | 2 |
| HTTP TCP/80 | 5 | 5 |
| Deny VLAN 10 | 4 | 4 |
| Deny other user VLAN | 4 | 4 |
| Deny VLAN 40 | 8 | 8 |
| Final permit | 4 | 4 |

The HTTPS rules have not yet been validated.

## 6. Verification Commands

```cisco
show access-lists
show ip interface vlan 20
show ip interface vlan 30
show running-config
```

These commands are used to verify ACL definitions, match counters, and inbound ACL assignments.

## 7. Pending Activities

The following tasks remain pending before completing Stage 6:

- Verify management VLAN restrictions.
- Perform final end-to-end security tests.
- Save and document the final configuration.


## 8. Current Status

**Status: In progress**

Extended ACLs have been implemented on VLAN 10, VLAN 20 and VLAN 30.

Validation confirms:

- VLAN 10 IT clients have unrestricted outbound access according to the implemented policy.
- VLAN 20 ADMIN and VLAN 30 OPERATIONS are restricted from initiating direct communication with other user VLANs and protected internal networks.
- DNS resolution and HTTP/HTTPS access to HT-APP01 work from VLAN 20 and VLAN 30.
- DHCP address assignment was validated after correcting the missing DHCP permit rule in ACL_VLAN10_IN.
- IT clients can initiate communication toward ADMIN and OPERATIONS, while unsolicited communication in the reverse direction remains restricted.

Management VLAN restrictions and final end-to-end security testing remain pending.