## OSPF ipv4

#### 1. Маршрутизаторы R14-R15 находятся в зоне 0 - backbone.

Создадим процесс OSPF
```
R14(config)#router ospf 1
R14(config-router)#router-id 10.10.0.3
R14(config-router)#passive-interface default
R14(config-router)#no passive-interface ethernet 0/0
R14(config-router)#no passive-interface ethernet 0/1
R14(config-router)#no passive-interface ethernet 0/3
R14(config-router)#no passive-interface lo0
R14(config-router)#network 10.10.0.105 0.0.0.1 area 0
R14(config-router)#network 10.10.0.115 0.0.0.1 area 0
R14(config-router)#network 10.10.0.116 0.0.0.1 area 101
R14(config-router)#network 10.10.0.3 0.0.0.0 area 0
```

```
R15(config)#router ospf 1
R15(config-router)#router-id 10.10.0.4
R15(config-router)#passive-interface default
R15(config-router)#no passive-interface ethernet 0/0
R15(config-router)#no passive-interface ethernet 0/1
R15(config-router)#no passive-interface ethernet 0/3
R15(config-router)#no passive-interface lo0
R15(config-router)#network 10.10.0.106 0.0.0.1 area 0
R15(config-router)#network 10.10.0.112 0.0.0.1 area 0
R15(config-router)#network 10.10.0.118 0.0.0.1 area 102
R15(config-router)#network 10.10.0.4 0.0.0.0 area 0
```

#### 1. Маршрутизаторы R12-R13(SW4-SW5) находятся в зоне 10. Дополнительно к маршрутам должны получать маршрут по умолчанию.

Добавим на R14 и R15 отправку маршрута по умолчанию
```
R14(config-router)#default-information originate
...
R15(config-router)#default-information originate
```

Создадим проццес OSPF 

```
R12(config)#router ospf 1
R12(config-router)#router-id 10.10.0.1
R12(config-router)#passive-interface default
R12(config-router)#no passive-interface ethernet 0/0
R12(config-router)#no passive-interface ethernet 0/1
R12(config-router)#no passive-interface ethernet 0/2
R12(config-router)#no passive-interface ethernet 0/3
R12(config-router)#no passive-interface lo0
R12(config-router)#network 10.10.0.1 0.0.0.0 area 10
R12(config-router)#network 10.10.0.100 0.0.0.1 area 10
R12(config-router)#network 10.10.0.102 0.0.0.1 area 10
R12(config-router)#network 10.10.0.104 0.0.0.1 area 0
R12(config-router)#network 10.10.0.106 0.0.0.1 area 0
```
```
SW4(config)#router ospf 1
SW4(config-router)#router-id 10.10.0.9
SW4(config-router)#passive-interface default
SW4(config-router)#no passive-interface ethernet 0/2
SW4(config-router)#no passive-interface ethernet 0/3
SW4(config-router)#no passive-interface ethernet 1/0
SW4(config-router)#no passive-interface ethernet 1/1
SW4(config-router)#no passive-interface lo0       
SW4(config-router)#no passive-interface vlan 10
SW4(config-router)#no passive-interface vlan 70
SW4(config-router)#network 10.0.0.9 0.0.0.0 area 10
SW4(config-router)#network 172.10.10.0 0.0.0.255 area 10
SW4(config-router)#network 172.10.70.0 0.0.0.255 area 10
SW4(config-router)#network 10.0.0.101 0.0.0.1 area 10   
SW4(config-router)#network 10.0.0.111 0.0.0.1 area 10
SW4(config-router)#network 10.0.0.120 0.0.0.1 area 10
SW4(config-router)#network 10.0.0.122 0.0.0.1 area 10
```

Настройка для R13 и SW5 идентичны.

Проверим, что SW4 получаем маршрут по умолчанию от OSPF:
```
SW4(config-router)#do show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is 172.10.10.253 to network 0.0.0.0

O*E2  0.0.0.0/0 [110/1] via 172.10.10.253, 00:00:09, Vlan10
      10.0.0.0/8 is variably subnetted, 24 subnets, 2 masks
O        10.10.0.1/32 [110/12] via 172.10.10.253, 00:00:09, Vlan10
O        10.10.0.2/32 [110/12] via 172.10.10.253, 00:00:09, Vlan10
O IA     10.10.0.3/32 [110/22] via 172.10.10.253, 00:00:09, Vlan10
O IA     10.10.0.4/32 [110/22] via 172.10.10.253, 00:00:09, Vlan10
O IA     10.10.0.5/32 [110/32] via 172.10.10.253, 00:00:09, Vlan10
O IA     10.10.0.6/32 [110/32] via 172.10.10.253, 00:00:09, Vlan10
C        10.10.0.9/32 is directly connected, Loopback0
O        10.10.0.10/32 [110/2] via 172.10.10.253, 00:00:09, Vlan10
C        10.10.0.100/31 is directly connected, Ethernet1/0
L        10.10.0.101/32 is directly connected, Ethernet1/0
O        10.10.0.102/31 [110/11] via 172.10.10.253, 00:00:09, Vlan10
O IA     10.10.0.104/31 [110/21] via 172.10.10.253, 00:00:09, Vlan10
O IA     10.10.0.106/31 [110/21] via 172.10.10.253, 00:00:09, Vlan10
O        10.10.0.108/31 [110/11] via 172.10.10.253, 00:00:09, Vlan10
C        10.10.0.110/31 is directly connected, Ethernet1/1
L        10.10.0.111/32 is directly connected, Ethernet1/1
O IA     10.10.0.112/31 [110/21] via 172.10.10.253, 00:00:09, Vlan10
O IA     10.10.0.114/31 [110/21] via 172.10.10.253, 00:00:09, Vlan10
O IA     10.10.0.116/31 [110/31] via 172.10.10.253, 00:00:09, Vlan10
O IA     10.10.0.118/31 [110/31] via 172.10.10.253, 00:00:09, Vlan10
C        10.10.0.120/31 is directly connected, Ethernet0/2
L        10.10.0.120/32 is directly connected, Ethernet0/2
C        10.10.0.122/31 is directly connected, Ethernet0/3
L        10.10.0.122/32 is directly connected, Ethernet0/3
      100.0.0.0/30 is subnetted, 2 subnets
O IA     100.100.10.0 [110/31] via 172.10.10.253, 00:00:09, Vlan10
O IA     100.100.11.0 [110/31] via 172.10.10.253, 00:00:09, Vlan10
      172.10.0.0/16 is variably subnetted, 4 subnets, 2 masks
C        172.10.10.0/24 is directly connected, Vlan10
L        172.10.10.252/32 is directly connected, Vlan10
C        172.10.70.0/24 is directly connected, Vlan70
L        172.10.70.252/32 is directly connected, Vlan70
```

#### 3. Маршрутизатор R19 находится в зоне 101 и получает только маршрут по умолчанию.

На R14 создадим prefix-list для фильтрации маршрутной информации, для получания только маршрута по умолчанию:
```
ip prefix-list ONLY_DEF seq 5 permit 0.0.0.0/0
ip prefix-list ONLY_DEF seq 10 deny 0.0.0.0/0 le 32
```
В процессе OSPF указываем нужную area и насправление:
```
router ospf 1
area 101 filter-list prefix ONLY_DEF in
```
Создадим проццес OSPF на R19:
```
R19(config)#router ospf 1
R19(config-router)#router-id 10.10.0.5
R19(config-router)#passive-interface default 
R19(config-router)#no passive-interface ethernet 0/0
R19(config-router)#no passive-interface lo0
R19(config-router)#network 10.10.0.5 0.0.0.0 area 101
R19(config-router)#network 10.10.0.117 0.0.0.1 area 101
```

Проверим таблицу маршрутизации:
```
R19#show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       + - replicated route, % - next hop override

Gateway of last resort is 10.10.0.116 to network 0.0.0.0

O*E2  0.0.0.0/0 [110/1] via 10.10.0.116, 02:38:18, Ethernet0/0
      10.0.0.0/8 is variably subnetted, 3 subnets, 2 masks
C        10.10.0.5/32 is directly connected, Loopback0
C        10.10.0.116/31 is directly connected, Ethernet0/0
L        10.10.0.117/32 is directly connected, Ethernet0/0
```

#### 4. Маршрутизатор R20 находится в зоне 102 и получает все маршруты, кроме маршрутов до сетей зоны 101

Создадим проццес OSPF на R20:
```
R20(config)#router ospf 1
R20(config-router)#router-id 10.10.0.6
R20(config-router)#passive-interface default
R20(config-router)#no passive-interface ethernet 0/0
R20(config-router)#no passive-interface lo0
R20(config-router)#network 10.10.0.6 0.0.0.0 area 102
R20(config-router)#network 10.10.0.119 0.0.0.1 area 102
```
Проверим таблицу маршрутизации:
```
R20(config-router)#do show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       + - replicated route, % - next hop override

Gateway of last resort is 10.10.0.118 to network 0.0.0.0

O*E2  0.0.0.0/0 [110/1] via 10.10.0.118, 00:00:19, Ethernet0/0
      10.0.0.0/8 is variably subnetted, 17 subnets, 2 masks
O IA     10.10.0.1/32 [110/21] via 10.10.0.118, 00:00:19, Ethernet0/0
O IA     10.10.0.2/32 [110/21] via 10.10.0.118, 00:00:19, Ethernet0/0
O IA     10.10.0.3/32 [110/31] via 10.10.0.118, 00:00:19, Ethernet0/0
O IA     10.10.0.4/32 [110/11] via 10.10.0.118, 00:00:19, Ethernet0/0
O IA     10.10.0.5/32 [110/41] via 10.10.0.118, 00:00:19, Ethernet0/0
C        10.10.0.6/32 is directly connected, Loopback0
O IA     10.10.0.100/31 [110/30] via 10.10.0.118, 00:00:19, Ethernet0/0
O IA     10.10.0.102/31 [110/30] via 10.10.0.118, 00:00:19, Ethernet0/0
O IA     10.10.0.104/31 [110/30] via 10.10.0.118, 00:00:19, Ethernet0/0
O IA     10.10.0.106/31 [110/20] via 10.10.0.118, 00:00:19, Ethernet0/0
O IA     10.10.0.108/31 [110/30] via 10.10.0.118, 00:00:19, Ethernet0/0
O IA     10.10.0.110/31 [110/30] via 10.10.0.118, 00:00:19, Ethernet0/0
O IA     10.10.0.112/31 [110/20] via 10.10.0.118, 00:00:19, Ethernet0/0
O IA     10.10.0.114/31 [110/30] via 10.10.0.118, 00:00:19, Ethernet0/0
O IA     10.10.0.116/31 [110/40] via 10.10.0.118, 00:00:19, Ethernet0/0
C        10.10.0.118/31 is directly connected, Ethernet0/0
L        10.10.0.119/32 is directly connected, Ethernet0/0
      100.0.0.0/30 is subnetted, 2 subnets
O IA     100.100.10.0 [110/40] via 10.10.0.118, 00:00:19, Ethernet0/0
O IA     100.100.11.0 [110/20] via 10.10.0.118, 00:00:19, Ethernet0/0
```

На R15 создадим prefix-list для фильтрации маршрутной информации, запрещающий сети из area 101:
```
ip prefix-list DENY_OSPF-101 seq 5 deny 10.10.0.5/32
ip prefix-list DENY_OSPF-101 seq 10 deny 10.10.0.116/31
ip prefix-list DENY_OSPF-101 seq 15 permit 0.0.0.0/0 le 32
```
В процессе OSPF указываем нужную area и насправление:
```
router ospf 1
area 102 filter-list prefix DENY_OSPF-101 in
```

Проверим таблицу маршрутизации на R20 ещё раз:
```
R20(config-router)#do show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       + - replicated route, % - next hop override

Gateway of last resort is 10.10.0.118 to network 0.0.0.0

O*E2  0.0.0.0/0 [110/1] via 10.10.0.118, 00:00:52, Ethernet0/0
      10.0.0.0/8 is variably subnetted, 15 subnets, 2 masks
O IA     10.10.0.1/32 [110/21] via 10.10.0.118, 00:00:52, Ethernet0/0
O IA     10.10.0.2/32 [110/21] via 10.10.0.118, 00:00:52, Ethernet0/0
O IA     10.10.0.3/32 [110/31] via 10.10.0.118, 00:00:52, Ethernet0/0
O IA     10.10.0.4/32 [110/11] via 10.10.0.118, 00:00:52, Ethernet0/0
C        10.10.0.6/32 is directly connected, Loopback0
O IA     10.10.0.100/31 [110/30] via 10.10.0.118, 00:00:52, Ethernet0/0
O IA     10.10.0.102/31 [110/30] via 10.10.0.118, 00:00:52, Ethernet0/0
O IA     10.10.0.104/31 [110/30] via 10.10.0.118, 00:00:52, Ethernet0/0
O IA     10.10.0.106/31 [110/20] via 10.10.0.118, 00:00:52, Ethernet0/0
O IA     10.10.0.108/31 [110/30] via 10.10.0.118, 00:00:52, Ethernet0/0
O IA     10.10.0.110/31 [110/30] via 10.10.0.118, 00:00:52, Ethernet0/0
O IA     10.10.0.112/31 [110/20] via 10.10.0.118, 00:00:52, Ethernet0/0
O IA     10.10.0.114/31 [110/30] via 10.10.0.118, 00:00:52, Ethernet0/0
C        10.10.0.118/31 is directly connected, Ethernet0/0
L        10.10.0.119/32 is directly connected, Ethernet0/0
      100.0.0.0/30 is subnetted, 2 subnets
O IA     100.100.10.0 [110/40] via 10.10.0.118, 00:00:52, Ethernet0/0
O IA     100.100.11.0 [110/20] via 10.10.0.118, 00:00:52, Ethernet0/0
```

Видим, что указынных сетей на R15 больше нету.
