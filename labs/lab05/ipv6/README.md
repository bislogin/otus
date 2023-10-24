## OSPF ipv6

#### 1. Маршрутизаторы R14-R15 находятся в зоне 0 - backbone.

Создадим процесс OSPF

На R14
```
ipv6 router ospf 1
 router-id 10.10.0.3
 passive-interface default
 no passive-interface Ethernet0/0
 no passive-interface Ethernet0/1
 no passive-interface Ethernet0/3
 no passive-interface Loopback0
!
interface Loopback0
 description == FOR MGMT ==
 ip address 10.10.0.3 255.255.255.255
 ipv6 address FE80::3 link-local
 ipv6 address 2001:AAAA::3/128
 ipv6 enable
 ipv6 ospf 1 area 0
 ipv6 ospf network point-to-point
!
interface Ethernet0/0
 description == link to R12 == 
 ip address 10.10.0.105 255.255.255.254
 ipv6 address FE80::69 link-local
 ipv6 address 2001:AAAA::69/127
 ipv6 enable
 ipv6 ospf 1 area 0
 ipv6 ospf network point-to-point
!
interface Ethernet0/1
 description == link to R13 == 
 ip address 10.10.0.115 255.255.255.254
 ipv6 address FE80::73 link-local
 ipv6 address 2001:AAAA::73/127
 ipv6 enable
 ipv6 ospf 1 area 0
 ipv6 ospf network point-to-point
!
interface Ethernet0/3
 description == link to R19 == 
 ip address 10.10.0.116 255.255.255.254
 ipv6 address FE80::74 link-local
 ipv6 address 2001:AAAA::74/127
 ipv6 enable
 ipv6 traffic-filter ONLY_DEF_v6 in
 ipv6 ospf 1 area 0
 ipv6 ospf network point-to-point
```
На R15
```
ipv6 router ospf 1
 router-id 10.10.0.4
 passive-interface default
 no passive-interface Ethernet0/0
 no passive-interface Ethernet0/1
 no passive-interface Ethernet0/3
 no passive-interface Loopback0
!
interface Loopback0
 description == FOR MGMT ==
 ip address 10.10.0.4 255.255.255.255
 ipv6 address FE80::4 link-local
 ipv6 address 2001:AAAA::4/128
 ipv6 enable
 ipv6 ospf 1 area 0
 ipv6 ospf network point-to-point
!
interface Ethernet0/0
 description == link to R13 == 
 ip address 10.10.0.113 255.255.255.254
 ipv6 address FE80::71 link-local
 ipv6 address 2001:AAAA::71/127
 ipv6 enable
 ipv6 ospf 1 area 0
 ipv6 ospf network point-to-point
!
interface Ethernet0/1
 description == link to R12 == 
 ip address 10.10.0.107 255.255.255.254
 ipv6 address FE80::6B link-local
 ipv6 address 2001:AAAA::6B/127
 ipv6 enable
 ipv6 ospf 1 area 0
 ipv6 ospf network point-to-point
!
interface Ethernet0/3
 description == link to R20 == 
 ip address 10.10.0.118 255.255.255.254
 ipv6 address FE80::76 link-local
 ipv6 address 2001:AAAA::76/127
 ipv6 enable
 ipv6 ospf 1 area 0
 ipv6 ospf network point-to-point

```

#### 1. Маршрутизаторы R12-R13(SW4-SW5) находятся в зоне 10. Дополнительно к маршрутам должны получать маршрут по умолчанию.

Добавим на R14 и R15 отправку маршрута по умолчанию

```
ipv6 router ospf 1
 default-information originate
```
Создадим процесс OSPF

```
ipv6 router ospf 1
 router-id 10.10.0.1
 passive-interface default
 no passive-interface Ethernet0/0
 no passive-interface Ethernet0/1
 no passive-interface Ethernet0/2
 no passive-interface Ethernet0/3
 no passive-interface Loopback0

interface Loopback0
 description == FOR MGMT == 
 ip address 10.10.0.1 255.255.255.255
 ipv6 address FE80::1 link-local
 ipv6 address 2001:AAAA::1/128
 ipv6 enable
 ipv6 ospf 1 area 10
 ipv6 ospf network point-to-point
!
interface Ethernet0/0
 description == link to SW4 ==
 ip address 10.10.0.100 255.255.255.254
 no ip redirects
 ipv6 address FE80::64 link-local
 ipv6 address 2001:AAAA::64/127
 ipv6 enable
 ipv6 ospf 1 area 10
 ipv6 ospf network point-to-point
 bfd interval 50 min_rx 50 multiplier 3
!
interface Ethernet0/1
 description == link to SW5 ==
 ip address 10.10.0.102 255.255.255.254
 ipv6 address FE80::66 link-local
 ipv6 address 2001:AAAA::66/127
 ipv6 enable
 ipv6 ospf 1 area 10
 ipv6 ospf network point-to-point
!
interface Ethernet0/2
 description == link to R14 == 
 ip address 10.10.0.104 255.255.255.254
 ipv6 address FE80::68 link-local
 ipv6 address 2001:AAAA::68/127
 ipv6 enable
 ipv6 ospf 1 area 0
 ipv6 ospf network point-to-point
!
interface Ethernet0/3
 description == link to R15 ==  
 ip address 10.10.0.106 255.255.255.254
 ipv6 address FE80::5A link-local
 ipv6 address 2001:AAAA::5A/127
 ipv6 enable
 ipv6 ospf 1 area 0
 ipv6 ospf network point-to-point
```

```
ipv6 router ospf 1
 router-id 10.10.0.9
 passive-interface default
 no passive-interface Ethernet0/2
 no passive-interface Ethernet0/3
 no passive-interface Ethernet1/0
 no passive-interface Ethernet1/1
 no passive-interface Loopback0
 no passive-interface Vlan10
 no passive-interface Vlan70

interface Loopback0
 description == FOR MGMT ==
 ip address 10.10.0.9 255.255.255.255
 ipv6 address FE80::9 link-local
 ipv6 address 2001:AAAA::9/128
 ipv6 enable
 ipv6 ospf 1 area 10
 ipv6 ospf network point-to-point
!
interface Ethernet0/2
 description == link to SW5 ==
 no switchport
 ip address 10.10.0.120 255.255.255.254
 duplex auto
 ipv6 address FE80::78 link-local
 ipv6 address 2001:AAAA::78/127
 ipv6 enable
 ipv6 ospf 1 area 10
 ipv6 ospf network point-to-point
!
interface Ethernet0/3
 description == link to SW5 ==
 no switchport
 ip address 10.10.0.122 255.255.255.254
 duplex auto
 ipv6 address FE80::7A link-local
 ipv6 address 2001:AAAA::7A/127
 ipv6 enable
 ipv6 ospf 1 area 10
 ipv6 ospf network point-to-point
!
interface Ethernet1/0
 description == link to R12 == 
 no switchport
 ip address 10.10.0.101 255.255.255.254
 no ip redirects
 duplex auto
 ipv6 address FE80::65 link-local
 ipv6 address 2001:AAAA::65/127
 ipv6 enable
 ipv6 ospf 1 area 10
 ipv6 ospf network point-to-point
 bfd interval 50 min_rx 50 multiplier 3
!
interface Ethernet1/1
 description == link to R13 == 
 no switchport
 ip address 10.10.0.111 255.255.255.254
 duplex auto
 ipv6 address FE80::6F link-local
 ipv6 address 2001:AAAA::6F/127
 ipv6 enable
 ipv6 ospf 1 area 10
 ipv6 ospf network point-to-point
 bfd template BFDT
!
interface Vlan10
 description == for USER1 ==
 ip address 172.10.10.252 255.255.255.0
 ipv6 address FE80::10:FC link-local
 ipv6 address 2001:AAAA::10:FC/112
 ipv6 enable
 ipv6 ospf 1 area 10
 ipv6 ospf network point-to-point
 vrrp 10 address-family ipv4
  address 172.10.10.254 primary
  exit-vrrp
 vrrp 10 address-family ipv6
  address FE80::10:FE primary
  address 2001:AAAA::10:FE/112
  exit-vrrp
!
interface Vlan70
 description == for USER7 ==
 ip address 172.10.70.252 255.255.255.0
 ipv6 address FE80::70:FC link-local
 ipv6 address 2001:AAAA::70:FC/112
 ipv6 enable
 ipv6 ospf 1 area 10
 ipv6 ospf network point-to-point
 vrrp 70 address-family ipv4
  priority 150
  address 172.10.70.254 primary
  exit-vrrp
 vrrp 70 address-family ipv6
  priority 150
  address FE80::70:FE primary
  address 2001:AAAA::70:FE/112
  exit-vrrp
```

Настройка для R13 и SW5 идентичны.

Проверим, что SW4 получаем маршрут по умолчанию от OSPF:
```
SW4#show ipv6 route
IPv6 Routing Table - default - 30 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
       B - BGP, R - RIP, I1 - ISIS L1, I2 - ISIS L2
       IA - ISIS interarea, IS - ISIS summary, D - EIGRP, EX - EIGRP external
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       RL - RPL, O - OSPF Intra, OI - OSPF Inter, OE1 - OSPF ext 1
       OE2 - OSPF ext 2, ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2
       a - Application
OE2 ::/0 [110/1], tag 1
     via FE80::64, Ethernet1/0
     via FE80::6E, Ethernet1/1
O   2001:AAAA::/127 [110/11]
     via FE80::64, Ethernet1/0
O   2001:AAAA::2/127 [110/11]
     via FE80::6E, Ethernet1/1
OI  2001:AAAA::4/127 [110/21]
     via FE80::6E, Ethernet1/1
     via FE80::64, Ethernet1/0
C   2001:AAAA::8/127 [0/0]
     via Loopback0, directly connected
L   2001:AAAA::9/128 [0/0]
     via Loopback0, receive
.......
```

#### 3. Маршрутизатор R19 находится в зоне 101 и получает только маршрут по умолчанию.

Поменяем на R19 тип area на total stub, чтобы внутрь проходил только маршрут по умолчанию:
```
area 101 stub no-summary
```

Создадим проццес OSPF, и добавим фильтрацию:
```
ipv6 router ospf 1
 router-id 10.10.0.5
 area 101 stub no-summary 
 passive-interface default
 no passive-interface Ethernet0/0
 no passive-interface Loopback0
!
interface Loopback0
 description == FOR MGMT ==
 ip address 10.10.0.5 255.255.255.255
 ipv6 address FE80::5 link-local
 ipv6 address 2001:AAAA::5/128
 ipv6 enable
 ipv6 ospf 1 area 101
 ipv6 ospf network point-to-point
!
interface Ethernet0/0
 description == link to R14 == 
 ip address 10.10.0.117 255.255.255.254
 ipv6 address FE80::75 link-local
 ipv6 address 2001:AAAA::75/127
 ipv6 enable
 ipv6 ospf 1 area 0
 ipv6 ospf network point-to-point
```

Проверим таблицу ospf database:
```
R19# show ipv6 ospf database 

            OSPFv3 Router with ID (10.10.0.5) (Process ID 1)

                Router Link States (Area 0)

ADV Router       Age         Seq#        Fragment ID  Link count  Bits
 10.10.0.1       1550        0x800000A5  0            2           B
 10.10.0.2       1441        0x800000A5  0            2           B
 10.10.0.3       1372        0x800000AC  0            3           E
 10.10.0.4       83          0x800000AB  0            3           E
 10.10.0.5       1448        0x80000009  0            1           B
 10.10.0.6       92          0x8000000B  0            1           B

                Inter Area Prefix Link States (Area 0)

ADV Router       Age         Seq#        Prefix
 10.10.0.1       1550        0x800000A3  2001:AAAA::66/127
 10.10.0.1       1550        0x800000A3  2001:AAAA::64/127
 10.10.0.1       1550        0x800000A3  2001:AAAA::6E/127
 10.10.0.1       1550        0x800000A3  2001:AAAA::7A/127
 10.10.0.1       1550        0x800000A3  2001:AAAA::78/127
 10.10.0.1       1550        0x800000A4  2001:AAAA::6C/127
 10.10.0.1       1550        0x800000A3  2001:AAAA::10:0/112
 10.10.0.1       1550        0x800000A3  2001:AAAA::70:0/112
 10.10.0.1       777         0x800000A2  2001:AAAA::9/128
 10.10.0.1       1550        0x800000A1  2001:AAAA::10/128
 10.10.0.1       1550        0x800000A1  2001:AAAA::1/128
 10.10.0.1       1550        0x800000A1  2001:AAAA::2/128
 10.10.0.2       1441        0x800000A3  2001:AAAA::6E/127
 10.10.0.2       1441        0x800000A3  2001:AAAA::6C/127
 10.10.0.2       925         0x800000A3  2001:AAAA::64/127
 10.10.0.2       925         0x800000A3  2001:AAAA::7A/127
 10.10.0.2       925         0x800000A3  2001:AAAA::78/127
 10.10.0.2       925         0x800000A4  2001:AAAA::66/127
 10.10.0.2       925         0x800000A3  2001:AAAA::10:0/112
 10.10.0.2       925         0x800000A3  2001:AAAA::70:0/112
 10.10.0.2       665         0x800000A2  2001:AAAA::9/128
 10.10.0.2       179         0x800000A2  2001:AAAA::10/128
 10.10.0.2       179         0x800000A2  2001:AAAA::1/128
 10.10.0.2       179         0x800000A2  2001:AAAA::2/128
 10.10.0.5       1448        0x80000009  2001:AAAA::5/128
 10.10.0.6       92          0x80000009  2001:AAAA::6/128

                Link (Type-8) Link States (Area 0)

ADV Router       Age         Seq#        Link ID    Interface
 10.10.0.3       1372        0x80000009  6          Et0/0
 10.10.0.5       1448        0x80000009  3          Et0/0

                Intra Area Prefix Link States (Area 0)

ADV Router       Age         Seq#        Link ID    Ref-lstype  Ref-LSID
 10.10.0.1       1550        0x800000A3  0          0x2001      0
 10.10.0.2       1441        0x800000A3  0          0x2001      0
 10.10.0.3       1372        0x800000A5  0          0x2001      0
 10.10.0.4       569         0x800000A7  0          0x2001      0
 10.10.0.5       1448        0x80000009  0          0x2001      0
 10.10.0.6       92          0x80000009  0          0x2001      0

                Router Link States (Area 101)

ADV Router       Age         Seq#        Fragment ID  Link count  Bits
 10.10.0.5       1206        0x800000AF  0            0           B

                Inter Area Prefix Link States (Area 101)

ADV Router       Age         Seq#        Prefix
 10.10.0.5       966         0x8000000A  ::/0

                Link (Type-8) Link States (Area 101)

ADV Router       Age         Seq#        Link ID    Interface
 10.10.0.5       1206        0x800000A6  10         Lo0

                Intra Area Prefix Link States (Area 101)

ADV Router       Age         Seq#        Link ID    Ref-lstype  Ref-LSID
 10.10.0.5       1206        0x800000AC  0          0x2001      0

                Type-5 AS External Link States

ADV Router       Age         Seq#        Prefix
 10.10.0.3       1879        0x800000A8  ::/0
```

#### 4. Маршрутизатор R20 находится в зоне 102 и получает все маршруты, кроме маршрутов до сетей зоны 101

Cоздадим prefix-list для фильтрации маршрутной информации, запрещающий сети из area 101:
```
ipv6 prefix-list DENY_OSPFv6-101 seq 5 deny 2001:AAAA::74/127
ipv6 prefix-list DENY_OSPFv6-101 seq 10 deny 2001:AAAA::5/128
ipv6 prefix-list DENY_OSPFv6-101 seq 15 permit ::/0 le 128
```

Создадим проццес OSPF:
```
ipv6 router ospf 1
 router-id 10.10.0.6
 distribute-list prefix-list DENY_OSPFv6-101 in
 passive-interface default
 no passive-interface Ethernet0/0
 no passive-interface Loopback0
!
interface Loopback0
 description == FOR MGMT ==
 ip address 10.10.0.6 255.255.255.255
 ipv6 address FE80::6 link-local
 ipv6 address 2001:AAAA::6/128
 ipv6 enable
 ipv6 ospf 1 area 102
 ipv6 ospf network point-to-point
!
interface Ethernet0/0
 description == link to R15 == 
 ip address 10.10.0.119 255.255.255.254
 ipv6 address FE80::77 link-local
 ipv6 address 2001:AAAA::77/127
 ipv6 enable
 ipv6 ospf 1 area 0
 ipv6 ospf network point-to-point
```

Проверим таблицу ospf database:
```
R20#  show ip ospf database 

            OSPF Router with ID (10.10.0.6) (Process ID 1)

                Router Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum Link count
10.10.0.1       10.10.0.1       1286        0x800000AD 0x006ED4 2
10.10.0.2       10.10.0.2       1883        0x800000AE 0x009E81 2
10.10.0.3       10.10.0.3       1375        0x800000C5 0x000FB2 4
10.10.0.4       10.10.0.4       30          0x800000B2 0x0020AD 4
10.10.0.5       10.10.0.5       1317        0x80000004 0x000CD5 1
10.10.0.6       10.10.0.6       12          0x80000003 0x002EAE 1

                Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
10.10.0.104     10.10.0.1       1286        0x80000003 0x0087F3
10.10.0.106     10.10.0.1       17          0x80000003 0x0081F6
10.10.0.112     10.10.0.2       1883        0x80000002 0x004B26
10.10.0.114     10.10.0.2       1357        0x80000003 0x002748
10.10.0.116     10.10.0.3       1375        0x80000003 0x003336
10.10.0.118     10.10.0.4       30          0x80000003 0x003133

                Summary Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
10.10.0.1       10.10.0.1       1806        0x800000A6 0x0092D9
10.10.0.1       10.10.0.2       1115        0x800000A4 0x0059FF
10.10.0.2       10.10.0.1       1542        0x800000A4 0x005504
10.10.0.2       10.10.0.2       1115        0x800000A7 0x0080E8
10.10.0.5       10.10.0.5       1317        0x80000003 0x00996E
10.10.0.6       10.10.0.6       12          0x80000003 0x00897C
10.10.0.10      10.10.0.1       1542        0x800000A4 0x00A0BA
10.10.0.10      10.10.0.2       1115        0x800000A4 0x009ABF
10.10.0.100     10.10.0.1       1806        0x800000A7 0x0003FC
10.10.0.100     10.10.0.2       1115        0x800000A4 0x00CB22
10.10.0.102     10.10.0.1       1806        0x800000A7 0x00EE0F
10.10.0.102     10.10.0.2       1115        0x800000A4 0x0053A2
10.10.0.108     10.10.0.1       1542        0x800000A4 0x001DD3
10.10.0.108     10.10.0.2       1115        0x800000A7 0x00AC4A
10.10.0.110     10.10.0.1       1542        0x800000A4 0x006D77
10.10.0.110     10.10.0.2       1115        0x800000A7 0x00985C
10.10.0.120     10.10.0.1       1542        0x800000A4 0x00A440
10.10.0.120     10.10.0.2       1115        0x800000A4 0x009E45
10.10.0.122     10.10.0.1       1542        0x800000A4 0x009052
10.10.0.122     10.10.0.2       1115        0x800000A4 0x008A57
172.10.10.0     10.10.0.1       1542        0x800000A4 0x005464
172.10.10.0     10.10.0.2       1115        0x800000A4 0x004E69
172.10.70.0     10.10.0.1       1542        0x800000A4 0x00BDBE
172.10.70.0     10.10.0.2       1115        0x800000A4 0x00B7C3

                Router Link States (Area 102)

Link ID         ADV Router      Age         Seq#       Checksum Link count
10.10.0.6       10.10.0.6       12          0x80000004 0x00C41B 1

                Summary Net Link States (Area 102)

Link ID         ADV Router      Age         Seq#       Checksum
10.10.0.1       10.10.0.6       12          0x80000003 0x008472
10.10.0.2       10.10.0.6       12          0x80000003 0x007A7B
10.10.0.3       10.10.0.6       12          0x80000003 0x00D416
10.10.0.4       10.10.0.6       12          0x80000003 0x0002FB
10.10.0.10      10.10.0.6       12          0x80000003 0x008E55
10.10.0.100     10.10.0.6       12          0x80000003 0x00F694
10.10.0.102     10.10.0.6       12          0x80000003 0x00E2A6
10.10.0.104     10.10.0.6       12          0x80000003 0x00CEB8
10.10.0.106     10.10.0.6       12          0x80000003 0x005639
10.10.0.108     10.10.0.6       12          0x80000003 0x00A6DC
10.10.0.110     10.10.0.6       12          0x80000003 0x0092EE
10.10.0.112     10.10.0.6       12          0x80000003 0x001A6F
10.10.0.114     10.10.0.6       12          0x80000003 0x006A13
10.10.0.118     10.10.0.6       12          0x80000003 0x007914
10.10.0.120     10.10.0.6       12          0x80000003 0x0092DA
10.10.0.122     10.10.0.6       12          0x80000003 0x007EEC
172.10.10.0     10.10.0.6       12          0x80000003 0x0042FE
172.10.70.0     10.10.0.6       12          0x80000003 0x00AB59

                Summary ASB Link States (Area 102)

Link ID         ADV Router      Age         Seq#       Checksum
10.10.0.3       10.10.0.6       12          0x80000003 0x00BC2E
10.10.0.4       10.10.0.6       12          0x80000003 0x00E914

                Type-5 AS External Link States

Link ID         ADV Router      Age         Seq#       Checksum Tag
0.0.0.0         10.10.0.3       1879        0x800000A6 0x0033C2 1
0.0.0.0         10.10.0.4       30          0x800000A8 0x0029C9 1
```
