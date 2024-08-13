
# Домашнее задание №7
-------
### VxLAN. L3VNI
-------

#### Задачи:
- ###### Настроить маршрутизацию в рамках Overlay между клиентами



***

## Схема сети:
![Схема сети:](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-7/pics/lba7.png)


## Конфигурация:
[brm-leaf-001](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-7/config/brm-leaf-001.conf)
```
! device: brm-leaf-001 (vEOS-lab, EOS-4.27.3F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname brm-leaf-001
!
spanning-tree mode mstp
!
vlan 10,20
!
vrf instance client
!
interface Ethernet1
   description brm-spn-001
   no switchport
   ip address 172.16.0.0/31
!
interface Ethernet2
   switchport access vlan 10
!
interface Ethernet3
   switchport access vlan 20
!
interface Ethernet4
!
interface Ethernet5
!
interface Ethernet6
!
interface Ethernet7
!
interface Ethernet8
!
interface Loopback0
   ip address 10.2.0.1/32
!
interface Management1
!
interface Vlan10
   vrf client
   ip address virtual 192.168.0.1/24
!
interface Vlan20
   vrf client
   ip address virtual 192.168.2.1/24
!
interface Vxlan1
   vxlan source-interface Loopback0
   vxlan udp-port 4789
   vxlan vlan 10 vni 10010
   vxlan vlan 20 vni 10020
   vxlan vrf client vni 6000
!
ip virtual-router mac-address 00:00:00:00:00:01
!
ip routing
ip routing vrf client
!
ip prefix-list Lupbacks seq 5 permit 10.2.0.0/24 le 32
!
mpls ip
!
route-map Lupbacks permit 10
   match ip address prefix-list Lupbacks
!
router bgp 65001
   router-id 10.2.0.1
   neighbor SPN_OVER peer group
   neighbor SPN_OVER update-source Loopback0
   neighbor SPN_OVER ebgp-multihop 2
   neighbor SPN_OVER send-community
   neighbor SPN_UNDER peer group
   neighbor 10.2.0.3 peer group SPN_OVER
   neighbor 10.2.0.3 remote-as 65002
   neighbor 172.16.0.1 peer group SPN_UNDER
   neighbor 172.16.0.1 remote-as 65002
   redistribute connected route-map Lupbacks
   !
   vlan 10
      rd 65001:10010
      route-target both 1:10010
      redistribute learned
   !
   vlan 20
      rd 65001:10020
      route-target both 1:10020
      redistribute learned
   !
   address-family evpn
      neighbor SPN_OVER activate
   !
   address-family ipv4
      no neighbor SPN_OVER activate
   !
   vrf client
      rd 65001:6000
      route-target import evpn 1:6000
      route-target export evpn 1:6000
!
end

```

[brm-leaf-002](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-7/config/brm-leaf-002.conf)
```
! device: brm-leaf-002 (vEOS-lab, EOS-4.27.3F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname brm-leaf-002
!
spanning-tree mode mstp
!
vlan 10,30
!
vrf instance client
!
interface Ethernet1
   description brm-spn-001
   no switchport
   ip address 172.16.0.3/31
!
interface Ethernet2
   switchport access vlan 30
!
interface Ethernet3
   switchport access vlan 10
!
interface Ethernet4
!
interface Ethernet5
!
interface Ethernet6
!
interface Ethernet7
!
interface Ethernet8
!
interface Loopback0
   ip address 10.2.0.2/32
!
interface Management1
!
interface Vlan10
   vrf client
   ip address virtual 192.168.0.1/24
!
interface Vlan30
   vrf client
   ip address virtual 192.168.3.1/24
!
interface Vxlan1
   vxlan source-interface Loopback0
   vxlan udp-port 4789
   vxlan vlan 10 vni 10010
   vxlan vlan 30 vni 10030
   vxlan vrf client vni 6000
!
ip virtual-router mac-address 00:00:00:00:00:01
!
ip routing
ip routing vrf client
!
ip prefix-list Lupbacks seq 5 permit 10.2.0.0/24 le 32
!
mpls ip
!
route-map Lupbacks permit 10
   match ip address prefix-list Lupbacks
!
router bgp 65003
   router-id 10.2.1.1
   neighbor LF_OVER peer group
   neighbor SPN_OVER peer group
   neighbor SPN_OVER update-source Loopback0
   neighbor SPN_OVER ebgp-multihop 2
   neighbor SPN_OVER send-community
   neighbor SPN_UNDER peer group
   neighbor 10.2.0.3 peer group SPN_OVER
   neighbor 10.2.0.3 remote-as 65002
   neighbor 172.16.0.2 peer group SPN_UNDER
   neighbor 172.16.0.2 remote-as 65002
   redistribute connected route-map Lupbacks
   !
   vlan 10
      rd 65003:10010
      route-target both 1:10010
      redistribute learned
   !
   vlan 30
      rd 65003:10030
      route-target both 1:10030
      redistribute learned
   !
   address-family evpn
      neighbor SPN_OVER activate
   !
   address-family ipv4
      no neighbor SPN_OVER activate
   !
   vrf client
      rd 65003:6000
      route-target import evpn 1:6000
      route-target export evpn 1:6000
!
end
```


[brm-spn-001](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-7/config/brm-spn-001.conf)
```
! device: brm-spn-001 (vEOS-lab, EOS-4.27.3F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname brm-spn-001
!
spanning-tree mode mstp
!
interface Ethernet1
   description brm-leaf-001
   no switchport
   ip address 172.16.0.1/31
!
interface Ethernet2
   description brm-leaf-002
   no switchport
   ip address 172.16.0.2/31
!
interface Ethernet3
!
interface Ethernet4
!
interface Ethernet5
!
interface Ethernet6
!
interface Ethernet7
!
interface Ethernet8
!
interface Loopback0
   ip address 10.2.0.3/32
!
interface Management1
!
ip routing
!
ip prefix-list Lupbacks seq 5 permit 10.2.0.0/24 le 32
!
mpls ip
!
route-map Lupbacks permit 10
   match ip address prefix-list Lupbacks
!
peer-filter LEAF_AS
   10 match as-range 65000-65100 result accept
!
router bgp 65002
   router-id 10.0.1.1
   neighbor 10.2.0.1 remote-as 65001
   neighbor 10.2.0.1 update-source Loopback0
   neighbor 10.2.0.1 ebgp-multihop 2
   neighbor 10.2.0.1 send-community
   neighbor 10.2.0.2 remote-as 65003
   neighbor 10.2.0.2 update-source Loopback0
   neighbor 10.2.0.2 ebgp-multihop 2
   neighbor 10.2.0.2 send-community
   neighbor 172.16.0.0 remote-as 65001
   neighbor 172.16.0.3 remote-as 65003
   redistribute connected route-map Lupbacks
   !
   address-family evpn
      neighbor 10.2.0.1 activate
      neighbor 10.2.0.2 activate
   !
   address-family ipv4
      no neighbor 10.2.0.1 activate
      no neighbor 10.2.0.2 activate
!
end

```

*** 

#### Проверка задания:

- brm-spn-001:
```
brm-spn-001#sh bgp evpn
BGP routing table information for VRF default
Router identifier 10.0.1.1, local AS number 65002
Route status codes: s - suppressed, * - valid, > - active, E - ECMP head, e - ECMP
                    S - Stale, c - Contributing to ECMP, b - backup
                    % - Pending BGP convergence
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  LocPref Weight  Path
 * >     RD: 65001:10010 mac-ip 0050.7966.6816
                                 10.2.0.1              -       100     0       65001 i
 * >     RD: 65001:10010 mac-ip 0050.7966.6816 192.168.0.3
                                 10.2.0.1              -       100     0       65001 i
 * >     RD: 65001:10020 mac-ip 0050.7966.6817
                                 10.2.0.1              -       100     0       65001 i
 * >     RD: 65001:10020 mac-ip 0050.7966.6817 192.168.2.3
                                 10.2.0.1              -       100     0       65001 i
 * >     RD: 65003:10030 mac-ip 0050.7966.6818
                                 10.2.0.2              -       100     0       65003 i
 * >     RD: 65003:10030 mac-ip 0050.7966.6818 192.168.3.3
                                 10.2.0.2              -       100     0       65003 i
 * >     RD: 65003:10010 mac-ip 0050.7966.6819
                                 10.2.0.2              -       100     0       65003 i
 * >     RD: 65003:10010 mac-ip 0050.7966.6819 192.168.0.4
                                 10.2.0.2              -       100     0       65003 i
 * >     RD: 65001:10010 imet 10.2.0.1
                                 10.2.0.1              -       100     0       65001 i
 * >     RD: 65001:10020 imet 10.2.0.1
                                 10.2.0.1              -       100     0       65001 i
 * >     RD: 65003:10010 imet 10.2.0.2
                                 10.2.0.2              -       100     0       65003 i
 * >     RD: 65003:10030 imet 10.2.0.2
                                 10.2.0.2              -       100     0       65003 i

```

- brm-leaf-002:
```
brm-leaf-002(config)#sh mac address-table
          Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports      Moves   Last Move
----    -----------       ----        -----      -----   ---------
  10    0050.7966.6816    DYNAMIC     Vx1        1       0:04:22 ago
  10    0050.7966.6819    DYNAMIC     Et3        1       0:03:26 ago
  30    0050.7966.6818    DYNAMIC     Et2        1       0:02:49 ago
4094    5019.0046.eff4    DYNAMIC     Vx1        1       0:04:22 ago
Total Mac Addresses for this criterion: 4

          Multicast Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       ----        -----
Total Mac Addresses for this criterion: 0
brm-leaf-002(config)#

```
- brm-leaf-001:
```
brm-leaf-001#sh mac address-table
          Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports      Moves   Last Move
----    -----------       ----        -----      -----   ---------
  10    0050.7966.6816    DYNAMIC     Et2        1       0:03:48 ago
  10    0050.7966.6819    DYNAMIC     Vx1        1       0:02:52 ago
  20    0050.7966.6817    DYNAMIC     Et3        1       0:03:13 ago
4094    5019.0084.4288    DYNAMIC     Vx1        1       0:02:52 ago
Total Mac Addresses for this criterion: 4

          Multicast Mac Address Table
------------------------------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       ----        -----
Total Mac Addresses for this criterion: 0

```
- pc1 (name 1):

```
1> sh ip

NAME        : 1[1]
IP/MASK     : 192.168.0.3/24
GATEWAY     : 192.168.0.1
DNS         :
MAC         : 00:50:79:66:68:16
LPORT       : 20000
RHOST:PORT  : 127.0.0.1:30000
MTU         : 1500

1> ping 192.168.3.3

84 bytes from 192.168.3.3 icmp_seq=1 ttl=62 time=21.291 ms
84 bytes from 192.168.3.3 icmp_seq=2 ttl=62 time=13.614 ms
84 bytes from 192.168.3.3 icmp_seq=3 ttl=62 time=15.681 ms
84 bytes from 192.168.3.3 icmp_seq=4 ttl=62 time=15.912 ms
84 bytes from 192.168.3.3 icmp_seq=5 ttl=62 time=18.690 ms

```
