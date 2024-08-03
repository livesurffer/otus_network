# Домашнее задание №1
-------
### Проектирование адресного пространства
-------

#### Задачи:
- ###### Собрать схему CLOS;
- ###### Распределить адресное пространство.

##### Распределение айпи адресов по следущей концепции:
10.Dn.Sn.X/31:

Dn - номер ЦОДа

Sn - номер Spine

X  - значение по порядку

Dn = 0...7:

0 - loopback 1

2 - p2p links

 
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

#### Схема сети:
![Схема сети:](https://github.com/livesurffer/otus_network/blob/main/Homework/homework-1/pics/Laba1.png)




