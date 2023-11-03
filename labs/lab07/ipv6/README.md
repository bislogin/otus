## EIGRP ipv6

#### В офисе С.-Петербург настроить EIGRP.

На каждом роутере и коммутаторе настраивам следуюшие базовые настройки, на примере R18:

Настроим ключ для аудентификации
```
key chain EIGRP-KEY
 key 1
  key-string keyeigrp
```

Создадим процесс EIGRP 
```
router eigrp SPB
 !
 address-family ipv6 unicast autonomous-system 20
  !
  af-interface default
   authentication mode md5
   authentication key-chain EIGRP-KEY
   bfd
   hello-interval 1
   hold-time 3
   passive-interface
  exit-af-interface
  !
  af-interface Loopback0
   no passive-interface
  exit-af-interface
  !
  af-interface Ethernet0/0
   no passive-interface
  exit-af-interface
  !
  af-interface Ethernet0/1
   no passive-interface
  exit-af-interface
  !
  topology base
  exit-af-topology
 exit-address-family
```

#### R32 получает только маршрут по умолчанию.

Для того, чтобы R32 получал только маршрут по умолчанию, на R16 и R17 в af-interface смотрящем в его сторону добавляем ``` summary-address ::/0```

```
 address-family ipv6 unicast autonomous-system 20
  !
  af-interface Ethernet0/3
   summary-address ::/0
   no passive-interface
  exit-af-interface
```

Проверим тамблице маршрутизации на R32
```
R32#show ipv6 route
IPv6 Routing Table - default - 7 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
       B - BGP, HA - Home Agent, MR - Mobile Router, R - RIP
       H - NHRP, I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea
       IS - ISIS summary, D - EIGRP, EX - EIGRP external, NM - NEMO
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF Intra, OI - OSPF Inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2, l - LISP
D   ::/0 [90/1024640]
     via FE80::5A, Ethernet0/0
     via FE80::76, Ethernet0/1
LC  2001:BBBB::4/128 [0/0]
     via Loopback0, receive
C   2001:BBBB::6A/127 [0/0]
     via Ethernet0/0, directly connected
L   2001:BBBB::6B/128 [0/0]
     via Ethernet0/0, receive
C   2001:BBBB::76/127 [0/0]
     via Ethernet0/1, directly connected
L   2001:BBBB::77/128 [0/0]
     via Ethernet0/1, receive
L   FF00::/8 [0/0]
     via Null0, receive
```

#### R16-17 анонсируют только суммарные префиксы.

Для того, чтобы R18 получал только суммарный маршрут, на R16 и R17 в af-interface смотрящем в его сторону добавляем ``` summary-address 2001:BBBB::/64```
```
router eigrp SPB
 !
 address-family ipv4 unicast autonomous-system 20
  !
  af-interface Ethernet0/1
   summary-address 2001:BBBB::/64
   no passive-interface
  exit-af-interface
```

Проверим тамблице маршрутизации на R18
```
R18#show ipv6 route eigrp 
IPv6 Routing Table - default - 11 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
       B - BGP, HA - Home Agent, MR - Mobile Router, R - RIP
       H - NHRP, I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea
       IS - ISIS summary, D - EIGRP, EX - EIGRP external, NM - NEMO
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF Intra, OI - OSPF Inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2, l - LISP
D   2001:BBBB::/64 [90/1024640]
     via FE80::6E, Ethernet0/1
     via FE80::66, Ethernet0/0
```

