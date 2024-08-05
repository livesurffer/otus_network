# Домашнее задание №2
-------
### Underlay. OSPF.
-------

#### Задачи:
- ###### Настроить OSPF для Underlay сети

***

## Схема сети:
![Схема сети:](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-1/pics/Laba2.png)


## Конфигурация:
[brm-leaf-001](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-1/config/brm-leaf-001.conf)
```
! device: brm-leaf-001 (vEOS-lab, EOS-4.27.3F)
!
! boot system flash:/vEOS-lab.swi
!
no aaa root
!
username admin role network-admin secret sha512 $6$PjXYd6pj7GfJ5Sja$0hUJjT0FDoHBoGG22V8jhadSdlpzmJeBFyHwGA6PtYTjuhQyEcFnr1Fc5EvSxdHcm3csDAlnV6q3KHQENc7o10
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
   mtu 9214
   no switchport
   ip address 10.2.1.0/31
!
interface Ethernet3
   description to Brm-spn-002
   mtu 9214
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
   ip address 10.1.0.2/32
!
interface Management1
!
no ip routing
!
end
````

[brm-leaf-002](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-1/config/brm-leaf-002.conf)
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
   mtu 9214
   no switchport
   ip address 10.2.1.2/31
!
interface Ethernet3
   description to Brm-spn-002
   mtu 9214
   no switchport
   ip address 10.2.2.3/31
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
interface Management1
!
no ip routing
!
end
```

[brm-leaf-003](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-1/config/brm-leaf-003.conf)
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
   mtu 9214
   no switchport
   ip address 10.2.2.4/31
!
interface Ethernet4
   description to Brm-spn-001
   mtu 9214
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
no ip routing
!
end
```

[brm-spn-001](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-1/config/brm-spn-001.conf)
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
   mtu 9214
   no switchport
   ip address 10.2.1.1/31
!
interface Ethernet2
   description to Brm-leaf-002
   mtu 9214
   no switchport
   ip address 10.2.1.3/31
!
interface Ethernet3
   description to Brm-leaf-003
   mtu 9214
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
   ip address 10.0.1.1/31
!
interface Management1
!
no ip routing
!
end
```


[brm-spn-002](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-1/config/brm-spn-002.conf)
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
   mtu 9214
   no switchport
   ip address 10.2.2.5/31
!
interface Ethernet2
   description to Brm-leaf-002
   mtu 9214
   no switchport
   ip address 10.2.2.3/31
!
interface Ethernet3
   description to Brm-leaf-001
   mtu 9214
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
no ip routing
!
end
```

*** 

#### Проверка задания:

- brm-spn-001
```
```

- brm-spn-002
```
```
