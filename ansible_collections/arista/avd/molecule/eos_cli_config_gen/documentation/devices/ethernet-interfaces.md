# ethernet-interfaces
# Table of Contents
<!-- toc -->

- [Management](#management)
  - [Management Interfaces](#management-interfaces)
- [Authentication](#authentication)
- [Monitoring](#monitoring)
- [Internal VLAN Allocation Policy](#internal-vlan-allocation-policy)
  - [Internal VLAN Allocation Policy Summary](#internal-vlan-allocation-policy-summary)
- [Interfaces](#interfaces)
  - [Ethernet Interfaces](#ethernet-interfaces)
- [Routing](#routing)
  - [IP Routing](#ip-routing)
  - [IPv6 Routing](#ipv6-routing)
- [BFD](#bfd)
  - [BFD Interfaces](#bfd-interfaces)
- [Multicast](#multicast)
- [Filters](#filters)
- [ACL](#acl)
- [Quality Of Service](#quality-of-service)

<!-- toc -->
# Management

## Management Interfaces

### Management Interfaces Summary

#### IPv4

| Management Interface | description | Type | VRF | IP Address | Gateway |
| -------------------- | ----------- | ---- | --- | ---------- | ------- |
| Management1 | oob_management | oob | MGMT | 10.73.255.122/24 | 10.73.255.2 |

#### IPv6

| Management Interface | description | Type | VRF | IPv6 Address | IPv6 Gateway |
| -------------------- | ----------- | ---- | --- | ------------ | ------------ |
| Management1 | oob_management | oob | MGMT | -  | - |

### Management Interfaces Device Configuration

```eos
!
interface Management1
   description oob_management
   vrf MGMT
   ip address 10.73.255.122/24
```

# Authentication

# Monitoring

# Internal VLAN Allocation Policy

## Internal VLAN Allocation Policy Summary

**Default Allocation Policy**

| Policy Allocation | Range Beginning | Range Ending |
| ------------------| --------------- | ------------ |
| ascending | 1006 | 4094 |

# Interfaces

## Ethernet Interfaces

### Ethernet Interfaces Summary

#### L2

| Interface | Description | Mode | VLANs | Native VLAN | Trunk Group | Channel-Group |
| --------- | ----------- | ---- | ----- | ----------- | ----------- | ------------- |
| Ethernet2 |  SRV-POD02_Eth1 | trunk | 110-111,210-211 | - | - | - |
| Ethernet4 |  Molecule IPv6 | access | - | - | - | - |
| Ethernet6 |  SRV-POD02_Eth1 | trunk | 110-111,210-211 | - | - | - |
| Ethernet7 |  Molecule L2 | access | - | - | - | - |

*Inherited from Port-Channel Interface

#### IPv4

| Interface | Description | Type | Channel Group | IP Address | VRF |  MTU | Shutdown | ACL In | ACL Out |
| --------- | ----------- | -----| ------------- | ---------- | ----| ---- | -------- | ------ | ------- |
| Ethernet1 | P2P_LINK_TO_DC1-SPINE1_Ethernet1 | routed | - | 172.31.255.1/31 | default | 1500 | - | - | - |
| Ethernet3 | P2P_LINK_TO_DC1-SPINE2_Ethernet2 | routed | - | 172.31.128.1/31 | default | 1500 | - | - | - |
| Ethernet8.101 | to WAN-ISP-01 Ethernet2.101 - VRF-C1 | l3dot1q | - | 172.31.128.1/31 | default | - | - | - | - |

#### IPv6

| Interface | Description | Type | Channel Group | IPv6 Address | VRF | MTU | Shutdown | ND RA Disabled | Managed Config Flag | IPv6 ACL In | IPv6 ACL Out |
| --------- | ----------- | ---- | --------------| ------------ | --- | --- | -------- | -------------- | -------------------| ----------- | ------------ |
| Ethernet3 | P2P_LINK_TO_DC1-SPINE2_Ethernet2 | routed | - | 2002:ABDC::1/64 | default | 1500 | - | - | *- | - | - |
| Ethernet4 | Molecule IPv6 | switchport | - | 2020::2020/64 | default | 9100 | true | true | true | IPv6_ACL_IN | IPv6_ACL_OUT |
| Ethernet8.101 | to WAN-ISP-01 Ethernet2.101 - VRF-C1 | l3dot1q | - | 2002:ABDC::1/64 | default | - | - | - | *- | - | - |

#### ISIS

| Interface | Channel Group | ISIS Instance | ISIS Metric | Mode |
| --------- | ------------- | ------------- | ----------- | ---- |
| Ethernet5 | - | ISIS_TEST | 99 | point-to-point |

### Ethernet Interfaces Device Configuration

```eos
!
interface Ethernet1
   description P2P_LINK_TO_DC1-SPINE1_Ethernet1
   mtu 1500
   no switchport
   ip address 172.31.255.1/31
   bfd interval 500 min-rx 500 multiplier 5
!
interface Ethernet2
   description SRV-POD02_Eth1
   switchport
   switchport trunk allowed vlan 110-111,210-211
   switchport mode trunk
   storm-control all level 10
   storm-control broadcast level pps 500
   storm-control unknown-unicast level 1
!
interface Ethernet3
   description P2P_LINK_TO_DC1-SPINE2_Ethernet2
   mtu 1500
   no switchport
   ip address 172.31.128.1/31
   ipv6 enable
   ipv6 address 2002:ABDC::1/64
   ipv6 nd prefix 2345:ABCD:3FE0::1/96 infinite 50 no-autoconfig
   ipv6 nd prefix 2345:ABCD:3FE0::2/96 50 infinite
   ipv6 nd prefix 2345:ABCD:3FE0::3/96 100000 no-autoconfig
!
interface Ethernet4
   description Molecule IPv6
   shutdown
   mtu 9100
   switchport
   ipv6 enable
   ipv6 address 2020::2020/64
   ipv6 address FE80:FEA::AB65/64 link-local
   ipv6 nd ra disabled
   ipv6 nd managed-config-flag
   ipv6 access-group IPv6_ACL_IN in
   ipv6 access-group IPv6_ACL_OUT out
!
interface Ethernet5
   description Molecule Routing
   no shutdown
   mtu 9100
   no switchport
   ip ospf network point-to-point
   ip ospf area 100
   ip ospf cost 99
   ip ospf authentication message-digest
   ip ospf authentication-key 7 asfddja23452
   ip ospf message-digest-key 1 sha512 7 asfddja23452
   isis enable ISIS_TEST
   isis metric 99
   isis network point-to-point
   pim ipv4 sparse-mode
!
interface Ethernet6
   description SRV-POD02_Eth1
   logging event link-status
   switchport
   switchport trunk allowed vlan 110-111,210-211
   switchport mode trunk
!
interface Ethernet7
   description Molecule L2
   no shutdown
   mtu 7000
   switchport
   qos trust cos
   qos cos 5
   spanning-tree portfast
   spanning-tree bpdufilter enable
   spanning-tree bpduguard enable
   vmtracer vmware-esx
   ptp enable
   ptp announce interval 10
   ptp announce timeout 30
   ptp delay-req interval 20
   ptp delay-mechanism p2p
   ptp sync-message interval 5
   ptp role master
   ptp vlan all
   ptp transport layer2
   service-profile QoS
   storm-control all level 75
   storm-control broadcast level pps 10
   storm-control multicast level 50
   storm-control unknown-unicast level 10
!
interface Ethernet8
   description to WAN-ISP1-01 Ethernet2
   no switchport
!
interface Ethernet8.101
   description to WAN-ISP-01 Ethernet2.101 - VRF-C1
   encapsulation dot1q vlan 101
   ip address 172.31.128.1/31
   ipv6 enable
   ipv6 address 2002:ABDC::1/64
```

# Routing

## IP Routing

### IP Routing Summary

| VRF | Routing Enabled |
| --- | --------------- |
| default | false|
### IP Routing Device Configuration

```eos
```
## IPv6 Routing

### IPv6 Routing Summary

| VRF | Routing Enabled |
| --- | --------------- |
| default | false |

# BFD

## BFD Interfaces

| Interface | Interval | Minimum RX | Multiplier |
| --------- | -------- | ---------- | ---------- |
| Ethernet1 | 500 | 500 | 5 |

# Multicast

# Filters

# ACL

# Quality Of Service
