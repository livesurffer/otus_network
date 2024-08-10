# Домашнее задание №3
-------
### Underlay. ISIS.
-------

#### Задачи:
- ###### Настроить IS-IS для Underlay сети


***

## Схема сети:
![Схема сети:](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-3/pics/laba3.png)


## Конфигурация:
[brm-leaf-001](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-3/config/brm-leaf-001.conf)
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
   mtu 9000
   no switchport
   ip address 10.2.1.0/31
   isis enable 1
   isis network point-to-point
!
interface Ethernet3
   description to Brm-spn-002
   mtu 9000
   no switchport
   ip address 10.2.2.0/31
   isis enable 1
   isis network point-to-point
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
   isis enable 1
!
interface Management1
!
ip routing
!
router isis 1
   net 49.0001.1720.1600.0221.00
   is-hostname brm-leaf-001
   is-type level-1
   log-adjacency-changes
   redistribute connected
   !
   address-family ipv4 unicast
!
end
````

[brm-leaf-002](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-3/config/brm-leaf-002.conf)
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
   isis enable 1
   isis network point-to-point
!
interface Ethernet3
   description to Brm-spn-002
   mtu 9000
   no switchport
   ip address 10.2.2.2/31
   isis enable 1
   isis network point-to-point
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
   isis enable 1
!
interface Management1
!
ip routing
!
router isis 1
   net 49.0001.1720.1600.0225.00
   is-hostname brm-leaf-002
   is-type level-1
   log-adjacency-changes
   redistribute connected
   !
   address-family ipv4 unicast
!
end
```
[brm-leaf-003](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-3/config/brm-leaf-003.conf)
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
   isis enable 1
   isis network point-to-point
!
interface Ethernet4
   description to Brm-spn-001
   mtu 9000
   no switchport
   ip address 10.2.1.4/31
   isis enable 1
   isis network point-to-point
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
   isis enable 1
!
interface Management1
!
ip routing
!
router isis 1
   net 49.0001.1720.1600.0224.00
   is-hostname brm-leaf-003
   is-type level-1
   log-adjacency-changes
   redistribute connected
   !
   address-family ipv4 unicast
!
end
```

[brm-spn-001](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-3/config/brm-spn-001.conf)
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
   mtu 9000
   no switchport
   ip address 10.2.1.1/31
   isis enable 1
   isis network point-to-point
!
interface Ethernet2
   description to Brm-leaf-002
   mtu 9000
   no switchport
   ip address 10.2.1.3/31
   isis enable 1
   isis network point-to-point
!
interface Ethernet3
   description to Brm-leaf-003
   mtu 9000
   no switchport
   ip address 10.2.1.5/31
   isis enable 1
   isis network point-to-point
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
   isis enable 1
!
interface Management1
!
ip routing
!
router isis 1
   net 49.0001.1720.1600.0223.00
   is-hostname brm-spn-001
   is-type level-1
   log-adjacency-changes
   redistribute connected
   !
   address-family ipv4 unicast
!
end
```


[brm-spn-002](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-3/config/brm-spn-002.conf)
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
   isis enable 1
   isis network point-to-point
!
interface Ethernet2
   description to Brm-leaf-002
   mtu 9000
   no switchport
   ip address 10.2.2.3/31
   isis enable 1
   isis network point-to-point
!
interface Ethernet3
   description to Brm-leaf-001
   mtu 9000
   no switchport
   ip address 10.2.2.1/31
   isis enable 1
   isis network point-to-point
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
   isis enable 1
!
interface Management1
!
ip routing
!
router isis 1
   net 49.0001.1720.1600.0222.00
   is-hostname brm-spn-002
   is-type level-1
   log-adjacency-changes
   redistribute connected
   !
   address-family ipv4 unicast
!
end
```

*** 

#### Проверка задания:

- brm-spn-001:
```
brm-spn-001#sh isis neighbors
Instance  VRF      System Id        Type Interface          SNPA              State Hold time   Circuit Id
1         default  brm-leaf-001     L1   Ethernet1          P2P               UP    28          0E
1         default  brm-leaf-003     L1   Ethernet3          P2P               UP    29          10
1         default  brm-leaf-002     L1   Ethernet2          P2P               UP    28          0E
```

- brm-spn-002:
```
brm-spn-002#sh isis neighbors
Instance  VRF      System Id        Type Interface          SNPA              State Hold time   Circuit Id
1         default  brm-leaf-001     L1   Ethernet3          P2P               UP    25          0F
1         default  brm-leaf-003     L1   Ethernet1          P2P               UP    23          0F
1         default  brm-leaf-002     L1   Ethernet2          P2P               UP    25          0F
```

- brm-leaf-001:
```
brm-leaf-001#sh isis neighbors
Instance  VRF      System Id        Type Interface          SNPA              State Hold time   Circuit Id
1         default  brm-spn-002      L1   Ethernet3          P2P               UP    23          0F
1         default  brm-spn-001      L1   Ethernet2          P2P               UP    24          0D
```

- brm-leaf-002:
```
brm-leaf-002#sh isis neighbors
Instance  VRF      System Id        Type Interface          SNPA              State Hold time   Circuit Id
1         default  brm-spn-002      L1   Ethernet3          P2P               UP    28          0E
1         default  brm-spn-001      L1   Ethernet2          P2P               UP    27          0E
```

- brm-leaf-003:
```
brm-leaf-003#sh isis neighbors
Instance  VRF      System Id        Type Interface          SNPA              State Hold time   Circuit Id
1         default  brm-spn-002      L1   Ethernet3          P2P               UP    21          0D
1         default  brm-spn-001      L1   Ethernet4          P2P               UP    24          0F
```