
# Домашнее задание №9
-------
### VxLAN. Routing.
-------

#### Задачи:
- ###### Реализовать передачу суммарных префиксов через EVPN


## Схема сети:
![Схема сети:](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-9/pics/laba9.png)


## Конфигурация:
[brm-leaf-001](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-9/config/brm-leaf-001.conf)
```
! device: brm-leaf-001 (vEOS-lab, EOS-4.27.3F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
username admin role network-admin secret sha512 $6$z.TJkV2efu34lwMM$3eZg0Xqi1I6PtVfmFyjHFvRDAs8k7EQx0w/eeev4MeJ1YWhFBDD3d0AzQKbcov5MSmwROOqOcn18AI0PgVf/O/
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
vrf instance B
!
vrf instance I
!
vrf instance K
!
interface Ethernet1
   description spine-01
   no switchport
   ip address 169.254.0.1/31
   ip ospf area 0.0.0.0
!
interface Ethernet2
   switchport access vlan 10
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
   description router-id
   ip address 10.0.1.1/32
   ip ospf area 0.0.0.0
!
interface Loopback1
   description vtep
   ip address 10.0.1.10/32
   ip ospf area 0.0.0.0
!
interface Management1
!
interface Vlan10
   ip address virtual 192.168.0.1/24
!
interface Vlan20
   ip address virtual 192.168.1.1/24
!
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 10 vni 100
   vxlan vlan 20 vni 200
   vxlan vrf K vni 7000
!
ip virtual-router mac-address 00:00:11:11:22:22
!
ip routing
no ip routing vrf B
ip routing vrf I
ip routing vrf K
!
router bgp 65000
   router-id 10.0.1.1
   neighbor OVERLAY peer group
   neighbor OVERLAY remote-as 65000
   neighbor OVERLAY update-source Loopback0
   neighbor OVERLAY send-community
   neighbor 10.0.2.1 peer group OVERLAY
   !
   vlan 10
   !
   vlan 20
      rd 10.0.1.10:200
      route-target both 200:20
      redistribute learned
   !
   address-family evpn
      neighbor OVERLAY activate
   !
   address-family ipv4
      no neighbor OVERLAY activate
   !
   vrf I
      rd 10.0.1.1:7000
      route-target import evpn 7000:700
      route-target export evpn 7000:700
   !
   vrf K
      rd 10.0.1.1:6000
      route-target import evpn 6000:600
      route-target export evpn 6000:600
!
router ospf 1
   router-id 10.0.1.1
   passive-interface Ethernet2
   passive-interface Ethernet3
   max-lsa 12000
!
end

```

[brm-leaf-002](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-9/config/brm-leaf-002.conf)
```
! device: brm-leaf-002 (vEOS-lab, EOS-4.27.3F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
username admin role network-admin secret sha512 $6$1ZqIZ38hgGcJqYgE$6OQh0BsLTHp1yWbxOwR34w9675WVWrt1T7IbM1y8dGNpFO5GXoFOc9nfgGXPJO70BDars1S9vVsNYU8Uveo8y/
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname brm-leaf-002
!
spanning-tree mode mstp
!
vlan 10,20,30
!
vrf instance I
!
vrf instance K
!
interface Ethernet1
   description spine-01
   no switchport
   ip address 169.254.0.3/31
   ip ospf area 0.0.0.0
!
interface Ethernet2
   switchport access vlan 10
!
interface Ethernet3
   switchport access vlan 30
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
   ip address 10.0.1.2/32
   ip ospf area 0.0.0.0
!
interface Loopback1
   ip address 10.0.1.20/32
   ip ospf area 0.0.0.0
!
interface Management1
!
interface Vlan10
   ip address virtual 192.168.0.1/24
!
interface Vlan30
   ip address virtual 192.168.2.1/24
!
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 10 vni 100
   vxlan vlan 30 vni 300
   vxlan vrf I vni 7000
   vxlan vrf K vni 6000
!
ip virtual-router mac-address 00:00:11:11:22:22
!
ip routing
no ip routing vrf I
no ip routing vrf K
!
mpls ip
!
router bgp 65000
   router-id 10.0.1.2
   neighbor OVERLAY peer group
   neighbor OVERLAY remote-as 65000
   neighbor OVERLAY update-source Loopback0
   neighbor OVERLAY send-community
   neighbor 10.0.2.1 peer group OVERLAY
   !
   vlan 10
      rd 10.0.1.20:100
      route-target both 100:10
      redistribute learned
   !
   vlan 30
      rd 10.0.1.20:300
      route-target both 300:30
      redistribute learned
   !
   address-family evpn
      neighbor OVERLAY activate
   !
   address-family ipv4
      no neighbor OVERLAY activate
   !
   vrf I
      route-target import evpn 7000:700
      route-target export evpn 7000:700
   !
   vrf K
      rd 10.0.1.2:6000
      route-target import evpn 6000:600
      route-target export evpn 6000:600
!
router ospf 1
   router-id 10.0.1.2
   passive-interface Ethernet2
   passive-interface Ethernet3
   max-lsa 12000
!
end

```
[brm-fw-001](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-9/config/brm-leaf-003.conf)
```
! device: brm-fw-001 (vEOS-lab, EOS-4.27.3F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model ribd
!
hostname brm-fw-001
!
spanning-tree mode mstp
!
interface Ethernet1
   no switchport
!
interface Ethernet1.100
   encapsulation dot1q vlan 100
   ip address 169.254.0.6/31
!
interface Ethernet1.200
   encapsulation dot1q vlan 200
   ip address 169.254.0.8/31
!
interface Ethernet2
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
   description router-id
   ip address 10.0.4.1/32
!
interface Management1
!
ip routing
!
mpls ip
!
router bgp 65001
   router-id 10.0.4.1
   neighbor 169.254.0.7 remote-as 65002
   neighbor 169.254.0.9 remote-as 65003
!
end

```


[brm-spn-002](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-9/config/brm-spn-002.conf)
```
! device: brm-spn-002 (vEOS-lab, EOS-4.27.3F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
username admin role network-admin secret sha512 $6$.6cpaBOFmiioWKiW$.uWGnBgHJiqDNtgQidhKvhzs0uv8ucNqrW3k7ZFUe/8/IwmazLbuojzA4y8AH7073zGhJ4zoSyX9vL7rI4bgS0
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
   description leaf-01
   no switchport
   ip address 169.254.0.0/31
   ip ospf area 0.0.0.0
!
interface Ethernet2
   description leaf-02
   no switchport
   ip address 169.254.0.2/31
   ip ospf area 0.0.0.0
!
interface Ethernet3
   description router-id
   no switchport
   ip address 169.254.0.4/31
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
   description router-id
   ip address 10.0.2.1/32
   ip ospf area 0.0.0.0
!
interface Management1
!
ip routing
!
mpls ip
!
peer-filter LEAF_AS_RANGE
   10 match as-range 65000-65010 result accept
!
router bgp 65000
   router-id 10.0.2.1
   bgp listen range 10.0.0.0/16 peer-group OVERLAY peer-filter LEAF_AS_RANGE
   neighbor OVERLAY peer group
   neighbor OVERLAY update-source Loopback0
   neighbor OVERLAY route-reflector-client
   neighbor OVERLAY send-community
   !
   address-family evpn
      neighbor OVERLAY activate
   !
   address-family ipv4
      no neighbor OVERLAY activate
!
router ospf 1
   router-id 10.0.2.1
   max-lsa 12000
!
end

```



[brm-br-001](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-9/config/brm-br-001.conf)
```
! device: brm-br-002 (vEOS-lab, EOS-4.27.3F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
username admin role network-admin secret sha512 $6$6EAFXg.ZDIfojLpv$Be4N/fXvq3SylJD45kg9ah13/Y5D5HyZ8QuJlrgg5t5xp5u.KDr3HUCZ8YWMIQGXylGvjCUTRHSLu//PT2PAS/
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname brm-br-002
!
spanning-tree mode mstp
!
vrf instance I
!
vrf instance K
!
interface Ethernet1
   description spine-01
   no switchport
   ip address 169.254.0.5/31
   ip ospf area 0.0.0.0
!
interface Ethernet2
   description firewall-01
   no switchport
   vrf K
   ip address 169.254.0.7/31
!
interface Ethernet2.200
   encapsulation dot1q vlan 200
   vrf I
   ip address 169.254.0.9/31
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
   description router-id
   ip address 10.0.3.1/32
   ip ospf area 0.0.0.0
!
interface Loopback1
   description vtep
   ip address 10.0.3.10/32
   ip ospf area 0.0.0.0
!
interface Management1
!
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vrf I vni 7000
   vxlan vrf K vni 6000
!
ip virtual-router mac-address 00:00:11:11:22:22
!
ip routing
ip routing vrf I
ip routing vrf K
!
mpls ip
!
router bgp 65000
   router-id 10.0.3.1
   neighbor OVERLAY peer group
   neighbor OVERLAY remote-as 65000
   neighbor OVERLAY update-source Loopback0
   neighbor OVERLAY send-community
   neighbor 10.0.2.1 peer group OVERLAY
   !
   address-family evpn
      neighbor OVERLAY activate
   !
   address-family ipv4
      no neighbor OVERLAY activate
   !
   vrf I
      rd 10.0.3.10:7000
      route-target import evpn 7000:700
      route-target export evpn 7000:700
      neighbor 169.254.0.8 remote-as 65001
      neighbor 169.254.0.8 local-as 65003 no-prepend replace-as
      aggregate-address 192.168.1.0/24 summary-only
      aggregate-address 192.168.2.0/24 summary-only
   !
   vrf K
      rd 10.0.3.10:6000
      route-target import evpn 6000:600
      route-target export evpn 6000:600
      neighbor 169.254.0.6 remote-as 65001
      neighbor 169.254.0.6 local-as 65002 no-prepend replace-as
      aggregate-address 192.168.2.0/24 summary-only
!
router ospf 1
   router-id 10.0.3.1
   passive-interface Ethernet2
   passive-interface Ethernet3
   max-lsa 12000
!
end
```

[brm-fw-001](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-9/config/brm-br-001.conf)
```
! device: brm-fw-001 (vEOS-lab, EOS-4.27.3F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model ribd
!
hostname brm-fw-001
!
spanning-tree mode mstp
!
interface Ethernet1
   no switchport
!
interface Ethernet1.100
   encapsulation dot1q vlan 100
   ip address 169.254.0.6/31
!
interface Ethernet1.200
   encapsulation dot1q vlan 200
   ip address 169.254.0.8/31
!
interface Ethernet2
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
   description router-id
   ip address 10.0.4.1/32
!
interface Management1
!
ip routing
!
mpls ip
!
router bgp 65001
   router-id 10.0.4.1
   neighbor 169.254.0.7 remote-as 65002
   neighbor 169.254.0.9 remote-as 65003
!
end
```