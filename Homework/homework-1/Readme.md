# Домашнее задание №1
-------
### Проектирование адресного пространства
-------

#### Задачи:
- ###### Собрать схему CLOS;
- ###### Распределить адресное пространство.

##### Распределение айпи адресов по следущей концепции:
***10.Dn.Sn.X/31:***

- Dn - номер ЦОДа

- Sn - номер Spine

- X  - значение по порядку

***Dn = 0-2:***

- 0 - loopback 1

- 2 - p2p links

 
| Hostname      | Interface | IP/mast     | Description     |
| ------------- | --------- | ----------- | --------------- |
|  Brm-leaf-001 | Loopback1 | 10.0.0.1/32 |                 |
|  Brm-leaf-002 | Loopback1 | 10.0.0.2/32 |                 |
|  Brm-leaf-003 | Loopback1 | 10.0.0.3/32 |                 |
|  Brm-spn-001  | Loopback1 | 10.0.1.1/32 |                 |
|  Brm-spn-002  | Loopback1 | 10.0.1.2/32 |                 |
|               |           |             |                 |
|  Brm-leaf-001 | Eth2      | 10.2.1.0/31 | to Brm-spn-001  |
|  Brm-leaf-001 | Eth3      | 10.2.2.0/31 | to Brm-spn-002  |
|  Brm-leaf-002 | Eth2      | 10.2.1.2/31 | to Brm-spn-001  |
|  Brm-leaf-002 | Eth3      | 10.2.2.2/31 | to Brm-spn-002  |
|  Brm-leaf-003 | Eth4      | 10.2.1.4/31 | to Brm-spn-001  |
|  Brm-leaf-003 | Eth3      | 10.2.2.4/31 | to Brm-spn-002  |
|               |           |             |                 |
|  Brm-spn-001  | Eth1      | 10.2.1.1/31 | to Brm-leaf-001 |
|  Brm-spn-001  | Eth2      | 10.2.1.3/31 | to Brm-leaf-002 |
|  Brm-spn-001  | Eth3      | 10.2.1.5/31 | to Brm-leaf-003 |
|  Brm-spn-002  | Eth1      | 10.2.2.5/31 | to Brm-leaf-003 |
|  Brm-spn-002  | Eth2      | 10.2.2.3/31 | to Brm-leaf-002 |
|  Brm-spn-002  | Eth3      | 10.2.2.1/31 | to Brm-leaf-002 |

## Схема сети:
![Схема сети:](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-1/pics/Laba1.png)

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

