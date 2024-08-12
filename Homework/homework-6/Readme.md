
# Домашнее задание №3
-------
### Overlay. VxLAN L2 VNI.
-------

#### Задачи:
- ###### Настроить BGP peering между Leaf и Spine в AF l2vpn evpn



***

## Схема сети:
![Схема сети:](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-5/pics/laba6.png)


## Конфигурация:
[brm-leaf-001](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-6/config/brm-leaf-001.conf)
```
! device: brm-leaf-001 (vEOS-lab, EOS-4.27.3F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
username admin role network-admin secret sha512 $6$PjXYd6pj7GfJ5Sja$0hUJjT0FDoHBoGG22V8jhadSdlpzmJeBFyHwGA6PtYTjuhQyEcFnr1Fc5EvSxdHcm3csDAlnV6q3KHQENc7o10
!
container-manager
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
interface Ethernet1
   description to-pc1
   mtu 9000
   switchport access vlan 10
!
interface Ethernet2
   description to Brm-spn-001
   mtu 9000
   no switchport
   ip address 10.2.1.0/31
!
interface Ethernet3
   description to Brm-spn-002
   mtu 9000
   no switchport
   ip address 10.2.2.0/31
!
interface Ethernet4
   description to-pc2
   mtu 9000
   switchport access vlan 20
!
interface Ethernet5
!
interface Ethernet6
!
interface Ethernet7
!
interface Ethernet8
!
interface Loopback1
   ip address 10.1.0.1/32
!
interface Management1
!
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 10 vni 10010
   vxlan vlan 20 vni 10020
!
ip routing
!
ip route 10.0.1.1/32 10.2.1.1
ip route 10.0.1.2/32 10.2.2.1
!
mpls ip
!
router bgp 65101
   router-id 10.1.0.1
   neighbor EVPN peer group
   neighbor EVPN remote-as 65200
   neighbor EVPN update-source Loopback1
   neighbor EVPN ebgp-multihop 2
   neighbor EVPN send-community extended
   neighbor 10.0.1.1 peer group EVPN
   neighbor 10.0.1.2 peer group EVPN
   redistribute connected
   !
   vlan 10
      rd 65001:10010
      route-target both 10:10010
      redistribute learned
   !
   vlan 20
      rd 65001:10020
      route-target both 20:10020
      redistribute learned
   !
   address-family evpn
      neighbor EVPN activate
!
end
```

[brm-leaf-002](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-6/config/brm-leaf-002.conf)
```
! device: brm-leaf-002 (vEOS-lab, EOS-4.27.3F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
username admin role network-admin secret sha512 $6$sBRv.YPgdXkCbIAq$AmTPJGga5Ca15dQ2kwDdWe3tyQB.kCJNkC5Q/o.oi81/2GLRjG7STKoM0Qccl0m1ftCoU7TZJ3rUxjdQCZZJl/
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname brm-leaf-002
!
spanning-tree mode mstp
!
vlan 10,20
!
interface Ethernet1
   mtu 9000
   switchport access vlan 20
!
interface Ethernet2
   description to Brm-spn-001
   mtu 9000
   no switchport
   ip address 10.2.1.2/31
!
interface Ethernet3
   description to Brm-spn-002
   mtu 9000
   no switchport
   ip address 10.2.2.2/31
!
interface Ethernet4
   mtu 9000
   switchport access vlan 10
!
interface Ethernet5
!
interface Ethernet6
!
interface Ethernet7
!
interface Ethernet8
!
interface Loopback1
   ip address 10.1.0.2/32
!
interface Management1
!
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 10 vni 10010
   vxlan vlan 20 vni 10020
!
ip routing
!
ip route 10.0.1.1/32 10.2.1.3
ip route 10.0.1.2/32 10.2.2.3
!
mpls ip
!
router bgp 65102
   router-id 10.1.0.2
   neighbor EVPN peer group
   neighbor EVPN remote-as 65200
   neighbor EVPN update-source Loopback1
   neighbor EVPN ebgp-multihop 2
   neighbor EVPN send-community extended
   neighbor 10.0.1.1 peer group EVPN
   neighbor 10.0.1.2 peer group EVPN
   redistribute connected
   !
   vlan 10
      rd 65002:10010
      route-target both 10:10010
      redistribute learned
   !
   vlan 20
      rd 65002:10020
      route-target both 20:10020
      redistribute learned
   !
   address-family evpn
      neighbor EVPN activate
!
end
```
[brm-leaf-003](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-6/config/brm-leaf-003.conf)
```
! device: brm-leaf-003 (vEOS-lab, EOS-4.27.3F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
username admin role network-admin secret sha512 $6$3D/3IbTgVXnuY1hm$kFaYpCLrITQNygptd6GrHkMu/lZOL9f8vhVYjjQMRHmA59oF09UF6ILJHI.XEWVn4xgOklbEjCnP.0EnuV5sK0
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname brm-leaf-003
!
spanning-tree mode mstp
!
vlan 10,20
!
interface Ethernet1
   description to-pc5
   mtu 9000
   switchport access vlan 10
!
interface Ethernet2
   mtu 9000
   switchport access vlan 20
!
interface Ethernet3
   description to Brm-spn-002
   mtu 9000
   no switchport
   ip address 10.2.2.4/31
!
interface Ethernet4
   description to Brm-spn-001
   mtu 9000
   no switchport
   ip address 10.2.1.4/31
!
interface Ethernet5
!
interface Ethernet6
!
interface Ethernet7
!
interface Ethernet8
!
interface Loopback1
   ip address 10.1.0.3/32
!
interface Management1
!
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 10 vni 10010
   vxlan vlan 20 vni 10020
!
ip routing
!
ip route 10.0.1.1/32 10.2.1.5
ip route 10.0.1.2/32 10.2.2.5
!
mpls ip
!
router bgp 65103
   router-id 10.1.0.3
   neighbor EVPN peer group
   neighbor EVPN remote-as 65200
   neighbor EVPN update-source Loopback1
   neighbor EVPN ebgp-multihop 2
   neighbor EVPN send-community extended
   neighbor 10.0.1.1 peer group EVPN
   neighbor 10.0.1.2 peer group EVPN
   redistribute connected
   !
   vlan 10
      rd 65003:10010
      route-target both 10:10010
      redistribute learned
   !
   vlan 20
      rd 65003:10020
      route-target both 20:10020
      redistribute learned
   !
   address-family evpn
      neighbor EVPN activate
!
end
```

[brm-spn-001](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-6/config/brm-spn-001.conf)
```
! device: brm-spn-001 (vEOS-lab, EOS-4.27.3F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
username admin role network-admin secret sha512 $6$kBN/xxkohsGJpVa2$2CBGF.0uoA10FKjZXCq3NIogcoWrOBH90VXgNVDkkl9TsAMV64f01n.uHBv6XPag6VbRmDQsTVMgbbnyN2Bpf/
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
   description to Brm-leaf-001
   no switchport
   ip address 10.2.1.1/31
!
interface Ethernet2
   description to Brm-leaf-002
   mtu 9000
   no switchport
   ip address 10.2.1.3/31
!
interface Ethernet3
   description to Brm-leaf-003
   mtu 9000
   no switchport
   ip address 10.2.1.5/31
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
interface Loopback1
   ip address 10.0.1.1/32
!
interface Management1
!
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 10 vni 10010
   vxlan vlan 20 vni 10020
!
ip routing
!
ip route 10.1.0.1/32 10.2.1.0
ip route 10.1.0.2/32 10.2.1.2
ip route 10.1.0.3/32 10.2.1.4
!
mpls ip
!
router bgp 65200
   router-id 10.0.1.1
   neighbor EVPN peer group
   neighbor 10.1.0.1 remote-as 65101
   neighbor 10.1.0.1 update-source Loopback1
   neighbor 10.1.0.1 ebgp-multihop 2
   neighbor 10.1.0.1 send-community extended
   neighbor 10.1.0.2 remote-as 65102
   neighbor 10.1.0.2 update-source Loopback1
   neighbor 10.1.0.2 ebgp-multihop 2
   neighbor 10.1.0.2 send-community extended
   neighbor 10.1.0.3 remote-as 65103
   neighbor 10.1.0.3 update-source Loopback1
   neighbor 10.1.0.3 ebgp-multihop 2
   neighbor 10.1.0.3 send-community extended
   redistribute connected
   !
   address-family evpn
      neighbor 10.1.0.1 activate
      neighbor 10.1.0.2 activate
      neighbor 10.1.0.3 activate
!
end
```


[brm-spn-002](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-5/config/brm-spn-002.conf)
```
! device: brm-spn-002 (vEOS-lab, EOS-4.27.3F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
username admin role network-admin secret sha512 $6$vpnOT69tbtUiuhiu$FwikzGqSbyGCl64xhijOQGzDPcjyPnrWgKlh4HfYf3R6ejiN0e2ioFSMoClZeRzfS4BND8yIhr1EQX44nvfxc1
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname brm-spn-002
!
spanning-tree mode mstp
!
interface Ethernet1
   description to Brm-leaf-003
   mtu 9000
   no switchport
   ip address 10.2.2.5/31
!
interface Ethernet2
   description to Brm-leaf-002
   mtu 9000
   no switchport
   ip address 10.2.2.3/31
!
interface Ethernet3
   description to Brm-leaf-001
   mtu 9000
   no switchport
   ip address 10.2.2.1/31
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
interface Loopback1
   ip address 10.0.1.2/32
!
interface Management1
!
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 10 vni 10010
   vxlan vlan 20 vni 10020
!
ip routing
!
ip route 10.1.0.1/32 10.2.2.0
ip route 10.1.0.2/32 10.2.2.2
ip route 10.1.0.3/32 10.2.2.4
!
mpls ip
!
router bgp 65200
   router-id 10.0.1.2
   neighbor EVPN peer group
   neighbor 10.1.0.1 remote-as 65101
   neighbor 10.1.0.1 update-source Loopback1
   neighbor 10.1.0.1 ebgp-multihop 2
   neighbor 10.1.0.1 send-community extended
   neighbor 10.1.0.2 remote-as 65102
   neighbor 10.1.0.2 update-source Loopback1
   neighbor 10.1.0.2 ebgp-multihop 2
   neighbor 10.1.0.2 send-community extended
   neighbor 10.1.0.3 remote-as 65103
   neighbor 10.1.0.3 update-source Loopback1
   neighbor 10.1.0.3 ebgp-multihop 2
   neighbor 10.1.0.3 send-community extended
   redistribute connected
   !
   address-family evpn
      neighbor 10.1.0.1 activate
      neighbor 10.1.0.2 activate
      neighbor 10.1.0.3 activate
!
end
```

*** 

#### Проверка задания:

- brm-spn-001:
```
brm-spn-001>sh bgp evpn
BGP routing table information for VRF default
Router identifier 10.0.1.1, local AS number 65200
Route status codes: s - suppressed, * - valid, > - active, E - ECMP head, e - ECMP
                    S - Stale, c - Contributing to ECMP, b - backup
                    % - Pending BGP convergence
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  LocPref Weight  Path
 * >     RD: 65001:10010 imet 10.1.0.1
                                 10.1.0.1              -       100     0       65101 i
 * >     RD: 65001:10020 imet 10.1.0.1
                                 10.1.0.1              -       100     0       65101 i
 * >     RD: 65002:10010 imet 10.1.0.2
                                 10.1.0.2              -       100     0       65102 i
 * >     RD: 65002:10020 imet 10.1.0.2
                                 10.1.0.2              -       100     0       65102 i
 * >     RD: 65003:10010 imet 10.1.0.3
                                 10.1.0.3              -       100     0       65103 i
 * >     RD: 65003:10020 imet 10.1.0.3
                                 10.1.0.3              -       100     0       65103 i

```

- brm-spn-002:
```
brm-spn-002#sh bgp evpn
BGP routing table information for VRF default
Router identifier 10.0.1.2, local AS number 65200
Route status codes: s - suppressed, * - valid, > - active, E - ECMP head, e - ECMP
                    S - Stale, c - Contributing to ECMP, b - backup
                    % - Pending BGP convergence
Origin codes: i - IGP, e - EGP, ? - incomplete
AS Path Attributes: Or-ID - Originator ID, C-LST - Cluster List, LL Nexthop - Link Local Nexthop

          Network                Next Hop              Metric  LocPref Weight  Path
 * >     RD: 65001:10010 imet 10.1.0.1
                                 10.1.0.1              -       100     0       65101 i
 * >     RD: 65001:10020 imet 10.1.0.1
                                 10.1.0.1              -       100     0       65101 i
 * >     RD: 65002:10010 imet 10.1.0.2
                                 10.1.0.2              -       100     0       65102 i
 * >     RD: 65002:10020 imet 10.1.0.2
                                 10.1.0.2              -       100     0       65102 i
 * >     RD: 65003:10010 imet 10.1.0.3
                                 10.1.0.3              -       100     0       65103 i
 * >     RD: 65003:10020 imet 10.1.0.3
                                 10.1.0.3              -       100     0       65103 i
```
