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
R14(config-router)#interface Loopback0
R14(config-if)#ip ospf 1 area 0
R14(config-if)#interface ethernet 0/0
R14(config-if)#ip ospf 1 area 0
R14(config-if)#interface ethernet 0/1
R14(config-if)#ip ospf 1 area 0
R14(config-if)#interface ethernet 0/3
R14(config-if)#ip ospf 1 area 0
```

```
R15(config)#router ospf 1
R15(config-router)#router-id 10.10.0.4
R15(config-router)#passive-interface default
R15(config-router)#no passive-interface ethernet 0/0
R15(config-router)#no passive-interface ethernet 0/1
R15(config-router)#no passive-interface ethernet 0/3
R15(config-router)#no passive-interface lo0
R15(config-router)#interface Loopback0
R15(config-if)#ip ospf 1 area 0
R15(config-if)#interface ethernet 0/0
R15(config-if)#ip ospf 1 area 0
R15(config-if)#interface ethernet 0/1
R15(config-if)#ip ospf 1 area 0
R15(config-if)#interface ethernet 0/3
R15(config-if)#ip ospf 1 area 0
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

Создадим проццес OSPF на R19:
```
R19(config)#router ospf 1
R19(config-router)#router-id 10.10.0.5
R19(config-router)#passive-interface default 
R19(config-router)#no passive-interface ethernet 0/0
R19(config-router)#no passive-interface lo0
R19(config-router)#area 101 stub no-summary
R19(config-router)#interface Loopback0
R19(config-if)#ip ospf 1 area 101
R19(config-if)#interface ethernet 0/0
R19(config-if)#ip ospf 1 area 0
```

Проверим таблицу ospf database:
```
R19#show ip ospf database 

            OSPF Router with ID (10.10.0.5) (Process ID 1)

                Router Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum Link count
10.10.0.1       10.10.0.1       895         0x800000AD 0x006ED4 2
10.10.0.2       10.10.0.2       1491        0x800000AE 0x009E81 2
10.10.0.3       10.10.0.3       982         0x800000C5 0x000FB2 4
10.10.0.4       10.10.0.4       1656        0x800000B1 0x0022AC 4
10.10.0.5       10.10.0.5       921         0x80000004 0x000CD5 1
10.10.0.6       10.10.0.6       1615        0x80000002 0x0030AD 1

                Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
10.10.0.104     10.10.0.1       895         0x80000003 0x0087F3
10.10.0.106     10.10.0.1       1661        0x80000002 0x0083F5
10.10.0.112     10.10.0.2       1491        0x80000002 0x004B26
10.10.0.114     10.10.0.2       966         0x80000003 0x002748
10.10.0.116     10.10.0.3       982         0x80000003 0x003336
10.10.0.118     10.10.0.4       1656        0x80000002 0x003332

                Summary Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
10.10.0.1       10.10.0.1       1415        0x800000A6 0x0092D9
10.10.0.1       10.10.0.2       723         0x800000A4 0x0059FF
10.10.0.2       10.10.0.1       1151        0x800000A4 0x005504
10.10.0.2       10.10.0.2       723         0x800000A7 0x0080E8
10.10.0.5       10.10.0.5       921         0x80000003 0x00996E
10.10.0.6       10.10.0.6       1615        0x80000002 0x008B7B
10.10.0.10      10.10.0.1       1151        0x800000A4 0x00A0BA
10.10.0.10      10.10.0.2       723         0x800000A4 0x009ABF
10.10.0.100     10.10.0.1       1415        0x800000A7 0x0003FC
10.10.0.100     10.10.0.2       723         0x800000A4 0x00CB22
10.10.0.102     10.10.0.1       1415        0x800000A7 0x00EE0F
10.10.0.102     10.10.0.2       723         0x800000A4 0x0053A2
10.10.0.108     10.10.0.1       1151        0x800000A4 0x001DD3
10.10.0.108     10.10.0.2       723         0x800000A7 0x00AC4A
10.10.0.110     10.10.0.1       1151        0x800000A4 0x006D77
10.10.0.110     10.10.0.2       723         0x800000A7 0x00985C
10.10.0.120     10.10.0.1       1151        0x800000A4 0x00A440
10.10.0.120     10.10.0.2       723         0x800000A4 0x009E45
10.10.0.122     10.10.0.1       1151        0x800000A4 0x009052
10.10.0.122     10.10.0.2       723         0x800000A4 0x008A57
172.10.10.0     10.10.0.1       1151        0x800000A4 0x005464
172.10.10.0     10.10.0.2       723         0x800000A4 0x004E69
172.10.70.0     10.10.0.1       1151        0x800000A4 0x00BDBE
172.10.70.0     10.10.0.2       723         0x800000A4 0x00B7C3

                Router Link States (Area 101)

Link ID         ADV Router      Age         Seq#       Checksum Link count
10.10.0.5       10.10.0.5       921         0x80000003 0x00EAFA 1

                Summary Net Link States (Area 101)

Link ID         ADV Router      Age         Seq#       Checksum
0.0.0.0         10.10.0.5       662         0x80000005 0x00E040

                Type-5 AS External Link States

Link ID         ADV Router      Age         Seq#       Checksum Tag
0.0.0.0         10.10.0.3       1486        0x800000A6 0x0033C2 1
0.0.0.0         10.10.0.4       1656        0x800000A7 0x002BC8 1
```
Видим, что для Area 101 у нас только маршрут по умолчанию.

#### 4. Маршрутизатор R20 находится в зоне 102 и получает все маршруты, кроме маршрутов до сетей зоны 101

Создадим на R20 prefix-list для фильтрации:
```
ip prefix-list DENY_OSPF-101 seq 5 deny 10.10.0.5/32
ip prefix-list DENY_OSPF-101 seq 10 deny 10.10.0.116/31
ip prefix-list DENY_OSPF-101 seq 15 permit 0.0.0.0/0 le 32
```

Создадим проццес OSPF на R20:
```
R20(config)#router ospf 1
R20(config-router)#router-id 10.10.0.6
R20(config-router)#passive-interface default
R20(config-router)#no passive-interface ethernet 0/0
R20(config-router)#no passive-interface lo0
R20(config-router)#area 102 filter-list prefix DENY_OSPF-101 in
R20(config-router)#interface Loopback0
R20(config-if)#ip ospf 1 area 101
R20(config-if)#interface ethernet 0/0
R20(config-if)#ip ospf 1 area 0
```

Cоздадим prefix-list для фильтрации маршрутной информации, запрещающий сети из area 101:
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

Проверим таблицу ospf database на R20:
```
R20# show ip ospf database 

            OSPF Router with ID (10.10.0.6) (Process ID 1)

                Router Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum Link count
10.10.0.1       10.10.0.1       1473        0x800000B3 0x0062DA 2
10.10.0.2       10.10.0.2       308         0x800000B5 0x009088 2
10.10.0.3       10.10.0.3       1634        0x800000CB 0x0003B8 4
10.10.0.4       10.10.0.4       282         0x800000B8 0x0014B3 4
10.10.0.5       10.10.0.5       1510        0x8000000A 0x00FFDB 1
10.10.0.6       10.10.0.6       268         0x80000009 0x0022B4 1

                Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
10.10.0.104     10.10.0.1       1473        0x80000009 0x007BF9
10.10.0.106     10.10.0.1       212         0x80000009 0x0075FC
10.10.0.112     10.10.0.2       308         0x80000009 0x003D2D
10.10.0.114     10.10.0.2       1591        0x80000009 0x001B4E
10.10.0.116     10.10.0.3       1633        0x80000009 0x00273C
10.10.0.118     10.10.0.4       282         0x80000009 0x002539

                Summary Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
10.10.0.1       10.10.0.1       1978        0x800000AC 0x0086DF
10.10.0.1       10.10.0.2       1327        0x800000AA 0x004D06
10.10.0.2       10.10.0.1       1978        0x800000AA 0x00490A
10.10.0.2       10.10.0.2       1327        0x800000AD 0x0074EE
10.10.0.5       10.10.0.5       1510        0x80000009 0x008D74
10.10.0.6       10.10.0.6       268         0x80000009 0x007D82
10.10.0.10      10.10.0.1       1978        0x800000AA 0x0094C0
10.10.0.10      10.10.0.2       1327        0x800000AA 0x008EC5
10.10.0.100     10.10.0.1       1978        0x800000AD 0x00F603
10.10.0.100     10.10.0.2       1327        0x800000AA 0x00BF28
10.10.0.102     10.10.0.1       1978        0x800000AD 0x00E215
10.10.0.102     10.10.0.2       1327        0x800000AA 0x0047A8
10.10.0.108     10.10.0.1       1978        0x800000AA 0x0011D9
10.10.0.108     10.10.0.2       1327        0x800000AD 0x00A050
10.10.0.110     10.10.0.1       1978        0x800000AA 0x00617D
10.10.0.110     10.10.0.2       1327        0x800000AD 0x008C62
10.10.0.120     10.10.0.1       1978        0x800000AA 0x009846
10.10.0.120     10.10.0.2       1327        0x800000AA 0x00924B
10.10.0.122     10.10.0.1       1978        0x800000AA 0x008458
10.10.0.122     10.10.0.2       1327        0x800000AA 0x007E5D
172.10.10.0     10.10.0.1       1978        0x800000AA 0x00486A
172.10.10.0     10.10.0.2       1327        0x800000AA 0x00426F
172.10.70.0     10.10.0.1       1978        0x800000AA 0x00B1C4
172.10.70.0     10.10.0.2       1327        0x800000AA 0x00ABC9

                Router Link States (Area 102)

Link ID         ADV Router      Age         Seq#       Checksum Link count
10.10.0.6       10.10.0.6       268         0x8000000A 0x00B821 1

                Summary Net Link States (Area 102)

Link ID         ADV Router      Age         Seq#       Checksum
10.10.0.1       10.10.0.6       268         0x80000009 0x007878
10.10.0.2       10.10.0.6       268         0x80000009 0x006E81
10.10.0.3       10.10.0.6       268         0x80000009 0x00C81C
10.10.0.4       10.10.0.6       268         0x80000009 0x00F502
10.10.0.10      10.10.0.6       268         0x80000009 0x00825B
10.10.0.100     10.10.0.6       268         0x80000009 0x00EA9A
10.10.0.102     10.10.0.6       268         0x80000009 0x00D6AC
10.10.0.104     10.10.0.6       268         0x80000009 0x00C2BE
10.10.0.106     10.10.0.6       268         0x80000009 0x004A3F
10.10.0.108     10.10.0.6       268         0x80000009 0x009AE2
10.10.0.110     10.10.0.6       268         0x80000009 0x0086F4
10.10.0.112     10.10.0.6       268         0x80000009 0x000E75
10.10.0.114     10.10.0.6       268         0x80000009 0x005E19
10.10.0.118     10.10.0.6       268         0x80000009 0x006D1A
10.10.0.120     10.10.0.6       268         0x80000009 0x0086E0
10.10.0.122     10.10.0.6       268         0x80000009 0x0072F2
172.10.10.0     10.10.0.6       268         0x80000009 0x003605
172.10.70.0     10.10.0.6       268         0x80000009 0x009F5F

                Summary ASB Link States (Area 102)

Link ID         ADV Router      Age         Seq#       Checksum
10.10.0.3       10.10.0.6       268         0x80000009 0x00B034
10.10.0.4       10.10.0.6       268         0x80000009 0x00DD1A

                Type-5 AS External Link States

Link ID         ADV Router      Age         Seq#       Checksum Tag
0.0.0.0         10.10.0.3       123         0x800000AD 0x0025C9 1
0.0.0.0         10.10.0.4       282         0x800000AE 0x001DCF 1
```

Видим, что указынных сетей на R20 в Area 102 нету.
