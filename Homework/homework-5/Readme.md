# Домашнее задание №3
-------
### Underlay. ISIS.
-------

#### Задачи:
- ###### Настроить BGP для Underlay сети.




***

## Схема сети:
![Схема сети:](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-4/pics/laba4.png)


## Конфигурация:
[brm-leaf-001](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-5/config/brm-leaf-001.conf)
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
service routing protocols model ribd
!
hostname brm-leaf-001
!
spanning-tree mode mstp
!
interface Ethernet1
!
interface Ethernet2
   description to Brm-spn-001
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
ip routing
!
router bgp 65101
   router-id 10.1.0.1
   neighbor 10.0.1.1 remote-as 65201
   neighbor 10.0.1.1 update-source Loopback1
   neighbor 10.0.1.1 ebgp-multihop 2
   neighbor 10.0.1.2 remote-as 65202
   neighbor 10.0.1.2 update-source Loopback1
   neighbor 10.0.1.2 ebgp-multihop 2
   network 10.1.0.1/32
!
router ospf 1
   router-id 10.1.0.1
   network 10.1.0.1/32 area 0.0.0.0
   network 10.2.1.0/31 area 0.0.0.0
   network 10.2.2.0/31 area 0.0.0.0
   max-lsa 12000
!
end
```

[brm-leaf-002](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-5/config/brm-leaf-002.conf)
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
service routing protocols model ribd
!
hostname brm-leaf-002
!
spanning-tree mode mstp
!
interface Ethernet1
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
ip routing
!
router bgp 65102
   router-id 10.1.0.2
   neighbor 10.0.1.1 remote-as 65201
   neighbor 10.0.1.1 update-source Loopback1
   neighbor 10.0.1.1 ebgp-multihop 2
   neighbor 10.0.1.2 remote-as 65202
   neighbor 10.0.1.2 update-source Loopback1
   neighbor 10.0.1.2 ebgp-multihop 2
   network 10.1.0.2/32
!
router ospf 1
   router-id 10.1.0.2
   network 10.1.0.2/32 area 0.0.0.0
   network 10.2.1.2/31 area 0.0.0.0
   network 10.2.2.2/31 area 0.0.0.0
   max-lsa 12000
!
end
```
[brm-leaf-003](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-5/config/brm-leaf-003.conf)
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
service routing protocols model ribd
!
hostname brm-leaf-003
!
spanning-tree mode mstp
!
interface Ethernet1
!
interface Ethernet2
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
ip routing
!
router bgp 65103
   router-id 10.1.0.3
   neighbor 10.0.1.1 remote-as 65201
   neighbor 10.0.1.1 update-source Loopback1
   neighbor 10.0.1.1 ebgp-multihop 2
   neighbor 10.0.1.2 remote-as 65202
   neighbor 10.0.1.2 update-source Loopback1
   neighbor 10.0.1.2 ebgp-multihop 2
   network 10.1.0.3/32
!
router ospf 1
   router-id 10.1.0.3
   network 10.1.0.3/32 area 0.0.0.0
   network 10.2.1.4/31 area 0.0.0.0
   network 10.2.2.4/31 area 0.0.0.0
   max-lsa 12000
!
end
```

[brm-spn-001](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-5/config/brm-spn-001.conf)
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
service routing protocols model ribd
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
ip routing
!
router bgp 65201
   router-id 10.0.1.1
   neighbor 10.1.0.1 remote-as 65101
   neighbor 10.1.0.1 update-source Loopback1
   neighbor 10.1.0.1 ebgp-multihop 2
   neighbor 10.1.0.2 remote-as 65102
   neighbor 10.1.0.2 update-source Loopback1
   neighbor 10.1.0.2 ebgp-multihop 2
   neighbor 10.1.0.3 remote-as 65103
   neighbor 10.1.0.3 update-source Loopback1
   neighbor 10.1.0.3 ebgp-multihop 2
   network 10.0.1.1/32
!
router ospf 1
   router-id 10.0.1.1
   network 10.0.1.1/32 area 0.0.0.0
   network 10.2.1.0/31 area 0.0.0.0
   network 10.2.1.2/31 area 0.0.0.0
   network 10.2.1.4/31 area 0.0.0.0
   max-lsa 12000
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
service routing protocols model ribd
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
ip routing
!
router bgp 65202
   router-id 10.0.1.2
   neighbor 10.1.0.1 remote-as 65101
   neighbor 10.1.0.1 update-source Loopback1
   neighbor 10.1.0.1 ebgp-multihop 2
   neighbor 10.1.0.2 remote-as 65102
   neighbor 10.1.0.2 update-source Loopback1
   neighbor 10.1.0.2 ebgp-multihop 2
   neighbor 10.1.0.3 remote-as 65103
   neighbor 10.1.0.3 update-source Loopback1
   neighbor 10.1.0.3 ebgp-multihop 2
   network 10.0.1.2/32
!
router ospf 1
   router-id 10.0.1.2
   network 10.0.1.2/32 area 0.0.0.0
   network 10.2.2.0/31 area 0.0.0.0
   network 10.2.2.2/31 area 0.0.0.0
   network 10.2.2.4/31 area 0.0.0.0
   max-lsa 12000
!
end
```

*** 

#### Проверка задания:

- brm-spn-001:
```
brm-spn-001#sh ip bgp summary
BGP summary information for VRF default
Router identifier 10.0.1.1, local AS number 65201
Neighbor Status Codes: m - Under maintenance
  Neighbor         V  AS           MsgRcvd   MsgSent  InQ OutQ  Up/Down State   PfxRcd PfxAcc
  10.1.0.1         4  65101             25        29    0    0 00:20:07 Estab   1      1
  10.1.0.2         4  65102             22        24    0    0 00:17:42 Estab   1      1
  10.1.0.3         4  65103             21        21    0    0 00:16:26 Estab   1      1
```

- brm-spn-002:
```
brm-spn-002#sh ip bgp su
BGP summary information for VRF default
Router identifier 10.0.1.2, local AS number 65202
Neighbor Status Codes: m - Under maintenance
  Neighbor         V  AS           MsgRcvd   MsgSent  InQ OutQ  Up/Down State   PfxRcd PfxAcc
  10.1.0.1         4  65101             25        25    0    0 00:20:28 Estab   1      1
  10.1.0.2         4  65102             23        24    0    0 00:18:33 Estab   1      1
  10.1.0.3         4  65103             21        21    0    0 00:16:55 Estab   1      1
```
