### Проект: Проектирование сетевой фабрики на основе VxLAN EVPN

#### Цель проекта

Цель проекта состоит в создании и реализации сетевой архитектуры, которая обеспечит связь между двумя независимыми дата-центрами (ЦОД "LEFT" и ЦОД "RIGHT") через VxLAN EVPN фабрику. Это должно включать:

1. Организацию надежной и безопасной связи между дата-центрами.
2. Реализацию отказоустойчивой маршрутизации и балансировки трафика.
3. Предоставление клиентам услуги L3VPN через VxLAN EVPN фабрику.

#### Описание проекта

Проект предполагает создание сети, которая будет использовать VxLAN EVPN для эффективной маршрутизации и изоляции данных в пределах дата-центров. Схема проекта включает следующие ключевые элементы:

1. Связь между дата-центрами:
   - Дата-центры "LEFT" и "RIGHT" соединены через публичные POP площадки, такие как M9 и 3Data. Эти площадки используются для создания выделенного канала связи между дата-центрами, что обеспечивает высокую надежность и конфиденциальность.

2. Клиентская сеть:
   - Клиенту предоставляются VLAN'ы в обоих дата-центрах. Эти VLAN'ы связаны с виртуальными маршрутизаторами через VRF (Virtual Routing and Forwarding). Это позволяет создать изолированное окружение для каждого клиента.

3. Маршрутизация через VxLAN EVPN:
   - Для передачи клиентских префиксов между дата-центрами используется VxLAN EVPN фабрика. Это позволяет эффективно управлять трафиком и обеспечивать нужную изоляцию данных клиента.

4. Отказоустойчивость и безопасность:
   - Использование VxLAN EVPN обеспечивает отказоустойчивую маршрутизацию и безопасность передачи данных. BGP (Border Gateway Protocol) используется для обмена маршрутами между дата-центрами, что позволяет гарантировать высокую надежность соединений.

#### Конфигурация

1. Конфигурация VLAN и VRF:

В каждом дата-центре создаются VLAN'ы для клиента и для связи между устройствами. Также создается VRF для изоляции маршрутов клиента.
vlan 100,1110,1120

vrf instance CUSTOMERS

interface Vlan100
   vrf CUSTOMERS
   ip address 172.16.1.254/23

interface Vlan1110
   mtu 9214
   vrf CUSTOMERS
   ip address 169.254.1.1/30

interface Vlan1120
   mtu 9214
   vrf CUSTOMERS
   ip address 169.254.1.5/30

2. Настройка интерфейсов:

Интерфейсы на коммутаторах и маршрутизаторах настраиваются для поддержки требуемого MTU и режима работы.
interface Port-Channel1
   description LEFT-pd01-leaf-01_Po1
   mtu 9214
   switchport trunk allowed vlan 1110
   switchport mode trunk

interface Port-Channel2
   description LEFT-pd01-leaf-02_Po1
   switchport trunk allowed vlan 1120
   switchport mode trunk

interface Ethernet1
   description LEFT-pd01-leaf-01_Eth1
   mtu 9214
   channel-group 1 mode active

interface Ethernet2
   description LEFT-pd01-leaf-02_Eth1
   mtu 9214
   channel-group 2 mode active

3. Настройка BGP:

BGP настраивается для обмена маршрутами между дата-центрами. В этом процессе используются маршрутные карты для фильтрации префиксов.
ip prefix-list CLIENT_NET seq 5 permit 172.16.0.0/23

route-map CLIENT_NET permit 10
   match ip address prefix-list CLIENT_NET

peer-filter LEAF
   10 match as-range 4200100102 result accept

router bgp 4200100101
   no bgp default ipv4-unicast
   neighbor CUSTOMERS peer group

   address-family ipv4
      neighbor CUSTOMERS activate

   vrf CUSTOMERS
      router-id 10.1.0.1
      bgp listen range 169.254.1.0/29 peer-group CUSTOMERS peer-filter LEAF
      redistribute connected route-map CLIENT_NET

4. Настройка VxLAN EVPN:

VxLAN EVPN настраивается для обеспечения виртуализации сети и сегментации трафика.
interface Loopback1
   description vtep
   ip address 10.1.1.10/32

interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vrf CUSTOMERS vni 11999

ip prefix-list LOOPBACKS seq 5 permit 10.1.1.0/24 eq 32

route-map LOOPBACKS permit 10
   match ip address prefix-list LOOPBACKS

5. Настройка SPINE-коммутаторов:

SPINE-коммутаторы используются для пересылки трафика и обеспечения связи между LEAF-коммутаторами.
interface Loopback0
   description router-id
   ip address 10.1.2.1/32
   ip prefix-list LOOPBACKS seq 5 permit 10.1.2.0/24 eq 32

route-map LOOPBACKS permit 10
   match ip address prefix-list LOOPBACKS

6. Настройка BORDER-коммутаторов:

BORDER-коммутаторы управляют входящими и исходящими маршрутами, обеспечивая связь с внешними сетями.
interface Loopback0
   description router-id
   ip address 10.1.3.1/32

interface Loopback1
   description vtep
   ip address 10.1.3.10/32

interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vrf CUSTOMERS vni 22999

ip prefix-list LOOPBACKS seq 10 permit 10.1.3.0/24 eq 32

route-map LOOPBACKS permit 10
   match ip address prefix-list LOOPBACKS

#### Проверка и тестирование

После завершения настройки важно провести проверку работоспособности сети. Это включает:

1. Проверку BGP-сессий: Убедитесь, что все BGP-сессии установлены и маршруты анонсируются корректно.
2. Тестирование связности: Проверьте возможность связи между виртуальными машинами в разных дата-центрах.
3. Проверку таблиц маршрутизации: Убедитесь, что все маршруты присутствуют в таблицах маршрутизации.

Проверка BGP и таблиц маршрутизации на гипервизорах должна подтвердить успешное установление маршрутов между дата-центрами и корректное распределение трафика.

Проект по созданию сетевой фабрики на основе VxLAN EVPN успешно выполнен, обеспечивая надежную и отказоустойчивую связь между дата-центрами, а также безопасность и изоляцию клиентских данных.
