## OSPF ipv4

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
 ipv6 ospf 1 area 10
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
 ipv6 ospf 1 area 101
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
 ipv6 ospf 1 area 10
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
 ipv6 ospf 1 area 102
 ipv6 ospf network point-to-point

```

#### 1. Маршрутизаторы R12-R13(SW4-SW5) находятся в зоне 10. Дополнительно к маршрутам должны получать маршрут по умолчанию.

Добавим на R14 и R15 отправку маршрута по умолчанию

```
ipv6 router ospf 1
 default-information originate
```
Создадим проццес OSPF

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

На R19 создадим prefix-list для фильтрации маршрутной информации, для получания только маршрута по умолчанию:
```
ipv6 prefix-list ONLY_DEF_v6 seq 5 permit ::/0
ipv6 prefix-list ONLY_DEF_v6 seq 10 deny ::/0 le 128
```

Создадим проццес OSPF, и добавим фильтрацию:
```
ipv6 router ospf 1
 router-id 10.10.0.5
 distribute-list prefix-list ONLY_DEF_v6 in Ethernet0/0 
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
 ipv6 ospf 1 area 101
 ipv6 ospf network point-to-point
```

Проверим таблицу маршрутизации:
```
R19#show ipv6 route
IPv6 Routing Table - default - 6 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
       B - BGP, HA - Home Agent, MR - Mobile Router, R - RIP
       H - NHRP, I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea
       IS - ISIS summary, D - EIGRP, EX - EIGRP external, NM - NEMO
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF Intra, OI - OSPF Inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2, l - LISP
OE2 ::/0 [110/1], tag 1
     via FE80::74, Ethernet0/0
C   2001:AAAA::4/127 [0/0]
     via Loopback0, directly connected
L   2001:AAAA::5/128 [0/0]
     via Loopback0, receive
C   2001:AAAA::74/127 [0/0]
     via Ethernet0/0, directly connected
L   2001:AAAA::75/128 [0/0]
     via Ethernet0/0, receive
L   FF00::/8 [0/0]
     via Null0, receive
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
 distribute-list prefix-list DENY_OSPFv6-101 in Ethernet0/0 
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
 ipv6 ospf 1 area 10
 ipv6 ospf network point-to-point
!
interface Ethernet0/0
 description == link to R15 == 
 ip address 10.10.0.119 255.255.255.254
 ipv6 address FE80::77 link-local
 ipv6 address 2001:AAAA::77/127
 ipv6 enable
 ipv6 ospf 1 area 102
 ipv6 ospf network point-to-point
```

Проверим таблицу маршрутизации:
```
R20(config-rtr)#do show ipv6 route                                        
IPv6 Routing Table - default - 24 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
       B - BGP, HA - Home Agent, MR - Mobile Router, R - RIP
       H - NHRP, I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea
       IS - ISIS summary, D - EIGRP, EX - EIGRP external, NM - NEMO
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF Intra, OI - OSPF Inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2, l - LISP
OE2 ::/0 [110/1], tag 1
     via FE80::76, Ethernet0/0
OI  2001:AAAA::1/128 [110/20]
     via FE80::76, Ethernet0/0
OI  2001:AAAA::2/128 [110/20]
     via FE80::76, Ethernet0/0
OI  2001:AAAA::3/128 [110/30]
     via FE80::76, Ethernet0/0
OI  2001:AAAA::4/128 [110/10]
     via FE80::76, Ethernet0/0
LC  2001:AAAA::6/128 [0/0]
     via Loopback0, receive
OI  2001:AAAA::9/128 [110/30]
     via FE80::76, Ethernet0/0
OI  2001:AAAA::10/128 [110/30]
     via FE80::76, Ethernet0/0
OI  2001:AAAA::5A/127 [110/30]
     via FE80::76, Ethernet0/0
OI  2001:AAAA::64/127 [110/30]
     via FE80::76, Ethernet0/0
OI  2001:AAAA::66/127 [110/30]
     via FE80::76, Ethernet0/0
OI  2001:AAAA::68/127 [110/30]
     via FE80::76, Ethernet0/0
OI  2001:AAAA::6A/127 [110/20]
     via FE80::76, Ethernet0/0
OI  2001:AAAA::6C/127 [110/30]
     via FE80::76, Ethernet0/0
OI  2001:AAAA::6E/127 [110/30]
     via FE80::76, Ethernet0/0
OI  2001:AAAA::70/127 [110/20]
     via FE80::76, Ethernet0/0
OI  2001:AAAA::72/127 [110/30]
     via FE80::76, Ethernet0/0
C   2001:AAAA::76/127 [0/0]
     via Ethernet0/0, directly connected
L   2001:AAAA::77/128 [0/0]
     via Ethernet0/0, receive
OI  2001:AAAA::78/127 [110/40]
     via FE80::76, Ethernet0/0
OI  2001:AAAA::7A/127 [110/40]
     via FE80::76, Ethernet0/0
OI  2001:AAAA::10:0/112 [110/31]
     via FE80::76, Ethernet0/0
OI  2001:AAAA::70:0/112 [110/31]
     via FE80::76, Ethernet0/0
L   FF00::/8 [0/0]
     via Null0, receive
```
