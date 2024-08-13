
# Домашнее задание №8
-------
### VxLAN. L3VNI
-------

#### Задачи:
- ###### Настроить отказоустойчивое подключение клиентов с использованием EVPN Multihoming


## Схема сети:
![Схема сети:](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-7/pics/lba7.png)


## Конфигурация:
[brm-leaf-001](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-8/config/brm-leaf-001.conf)
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
interface Port-Channel1
   switchport trunk allowed vlan 10
   switchport mode trunk
!
interface Port-Channel2
   switchport trunk allowed vlan 20
   switchport mode trunk
!
interface Ethernet1
   description brm-spn-001
   no switchport
   ip address 172.16.0.0/31
   ip ospf area 0.0.0.0
!
interface Ethernet2
   switchport access vlan 10
   channel-group 1 mode active
!
interface Ethernet3
   switchport access vlan 20
   channel-group 2 mode active
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
   ip ospf area 0.0.0.0
!
interface Loopback1
   ip address 10.2.1.1/32
   ip ospf area 0.0.0.0
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
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 10 vni 10010
   vxlan vlan 20 vni 10020
   vxlan vrf client vni 6000
!
ip virtual-router mac-address 00:00:11:11:22:22
!
ip routing
ip routing vrf client
!
mpls ip
!
router bgp 65001
   router-id 10.2.0.1
   neighbor SPINE_OVERLAY peer group
   neighbor SPN_OVER peer group
   neighbor SPN_OVER update-source Loopback0
   neighbor SPN_OVER send-community
   neighbor SPN_UNDER peer group
   neighbor 10.2.0.3 peer group SPINE_OVERLAY
   redistribute connected route-map Lupbacks
   !
   vlan 10
      rd 65001:10010
      route-target both 1:10010
      route-target both 10:10010
      redistribute learned
   !
   vlan 20
      rd 65001:10020
      route-target both 20:10020
      route-target both 1:10020
      redistribute learned
   !
   address-family evpn
      neighbor SPINE_OVERLAY activate
   !
   address-family ipv4
      no neighbor SPINE_OVERLAY activate
   !
   vrf client
      rd 65001:6000
      route-target import evpn 1:6000
      route-target export evpn 1:6000
!
router ospf 1
   router-id 10.2.0.1
   max-lsa 12000
!
end

```

[brm-leaf-002](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-8/config/brm-leaf-002.conf)
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
vlan 10,20
!
vrf instance client
!
interface Port-Channel1
   switchport trunk allowed vlan 10,20
   switchport mode trunk
   !
   evpn ethernet-segment
      identifier 0000:1111:1111:1111:0000
      route-target import 11:11:11:11:11:11
   lacp system-id 1111.1111.1111
!
interface Ethernet1
   description brm-spn-001
   no switchport
   ip address 172.16.0.3/31
   ip ospf area 0.0.0.0
!
interface Ethernet2
   switchport access vlan 30
   channel-group 1 mode active
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
   ip ospf area 0.0.0.0
!
interface Loopback1
   ip address 10.2.1.2/32
   ip ospf area 0.0.0.0
!
interface Management1
!
interface Vlan10
   vrf client
   ip address virtual 192.168.0.1/24
!
interface Vlan20
   ip address 192.168.2.1/24
!
interface Vlan30
   vrf client
   ip address virtual 192.168.3.1/24
!
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 10 vni 10010
   vxlan vlan 20 vni 10020
   vxlan vlan 30 vni 10030
   vxlan vrf client vni 6000
!
ip virtual-router mac-address 00:00:11:11:22:22
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
   router-id 10.2.0.2
   neighbor SPINE_OVERLAY peer group
   neighbor SPINE_OVERLAY remote-as 65001
   neighbor SPINE_OVERLAY update-source Loopback0
   neighbor SPINE_OVERLAY send-community
   neighbor 10.2.0.3 peer group SPINE_OVERLAY
   !
   vlan 10
      rd 65001:10010
      route-target both 10:10010
      route-target both 1:10010
      redistribute learned
   !
   vlan 20
      rd 65001:10020
      route-target both 20:10020
      route-target both 1:10020
      redistribute learned
   !
   address-family evpn
      neighbor SPINE_OVERLAY activate
   !
   address-family ipv4
      no neighbor SPINE_OVERLAY activate
   !
   vrf client
      rd 65001:6000
      route-target import evpn 1:6000
      route-target export evpn 1:6000
!
router ospf 1
   router-id 10.2.0.2
   max-lsa 12000
!
end

```
[brm-leaf-003](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-8/config/brm-leaf-003.conf)
```
! device: brm-leaf-003 (vEOS-lab, EOS-4.27.3F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
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
vrf instance client
!
interface Port-Channel1
   switchport trunk allowed vlan 10,20
   switchport mode trunk
   !
   evpn ethernet-segment
      identifier 0000:1111:1111:1111:0000
      route-target import 11:11:11:11:11:11
   lacp system-id 1111.1111.1111
!
interface Ethernet1
   no switchport
   ip address 172.16.0.4/31
   ip ospf area 0.0.0.0
!
interface Ethernet2
   channel-group 1 mode active
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
   ip address 10.2.0.4/32
   ip ospf area 0.0.0.0
!
interface Loopback1
   ip address 10.2.1.4/32
   ip ospf area 0.0.0.0
!
interface Management1
!
interface Vlan10
   ip address virtual 192.168.0.1/24
!
interface Vlan20
   ip address virtual 192.168.2.1/24
!
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 10 vni 10010
   vxlan vlan 20 vni 10020
   vxlan vrf client vni 6000
!
ip virtual-router mac-address 00:00:11:11:22:22
!
ip routing
ip routing vrf client
!
router bgp 65001
   router-id 10.2.0.4
   neighbor SPINE_OVERLAY peer group
   neighbor SPINE_OVERLAY remote-as 65001
   neighbor SPINE_OVERLAY update-source Loopback0
   neighbor SPINE_OVERLAY send-community
   neighbor 10.2.0.3 peer group SPINE_OVERLAY
   !
   vlan 10
      rd 65001:10010
      route-target both 1:10010
      route-target both 10:10010
      redistribute learned
   !
   vlan 20
      rd 65001:10020
      route-target both 1:10020
      route-target both 20:10020
      redistribute learned
   !
   address-family evpn
      neighbor SPINE_OVERLAY activate
   !
   address-family ipv4
      no neighbor SPINE_OVERLAY activate
   !
   vrf client
      rd 65001:6000
      route-target import evpn 1:6000
      route-target export evpn 1:6000
!
router ospf 1
   router-id 10.2.0.4
   max-lsa 12000
!
end
```


[brm-spn-001](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-8/config/brm-spn-001.conf)
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
   ip ospf area 0.0.0.0
!
interface Ethernet2
   description brm-leaf-002
   no switchport
   ip address 172.16.0.2/31
   ip ospf area 0.0.0.0
!
interface Ethernet3
   description brm-leaf-003
   no switchport
   ip address 172.16.0.5/31
   ip ospf area 0.0.0.0
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
   ip ospf area 0.0.0.0
!
interface Management1
!
ip routing
!
mpls ip
!
router bgp 65001
   router-id 10.2.0.3
   bgp listen range 10.2.0.0/16 peer-group LEAF_OVERLAY remote-as 65001
   neighbor LEAF_OVERLAY peer group
   neighbor LEAF_OVERLAY update-source Loopback0
   neighbor LEAF_OVERLAY route-reflector-client
   neighbor LEAF_OVERLAY send-community
   !
   address-family evpn
      neighbor LEAF_OVERLAY activate
   !
   address-family ipv4
      no neighbor LEAF_OVERLAY activate
!
router ospf 1
   router-id 10.2.0.3
   max-lsa 12000
!
end

```
