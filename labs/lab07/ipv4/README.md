## EIGRP ipv4

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
 address-family ipv4 unicast autonomous-system 20
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
  network 10.20.0.0 0.0.255.255
  network 10.20.0.3 0.0.0.0
  eigrp router-id 10.20.0.3
 exit-address-family
```
Обявляем все порты пассивными, кроме нужных. Так же объявляем сети, наш loopback и суммированную сеть офиса.


#### R32 получает только маршрут по умолчанию.

Для того, чтобы R32 получал только маршрут по умолчанию, на R16 и R17 в af-interface смотрящем в его сторону добавляем ``` summary-address 0.0.0.0 0.0.0.0```

```
router eigrp SPB
 !
 address-family ipv4 unicast autonomous-system 20
  !
  af-interface Ethernet0/3
   summary-address 0.0.0.0 0.0.0.0
   no passive-interface
  exit-af-interface
```

Проверим тамблице маршрутизации на R32
```
R32#show ip route 
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       + - replicated route, % - next hop override

Gateway of last resort is 10.20.0.118 to network 0.0.0.0

D*    0.0.0.0/0 [90/1024640] via 10.20.0.118, 00:27:34, Ethernet0/1
                [90/1024640] via 10.20.0.106, 00:27:34, Ethernet0/0
      10.0.0.0/8 is variably subnetted, 5 subnets, 2 masks
C        10.20.0.4/32 is directly connected, Loopback0
C        10.20.0.106/31 is directly connected, Ethernet0/0
L        10.20.0.107/32 is directly connected, Ethernet0/0
C        10.20.0.118/31 is directly connected, Ethernet0/1
L        10.20.0.119/32 is directly connected, Ethernet0/1
```

#### R16-17 анонсируют только суммарные префиксы.

Для того, чтобы R18 получал только суммарный маршрут, на R16 и R17 в af-interface смотрящем в его сторону добавляем ``` summary-address 10.20.0.0 255.255.0.0```
```
router eigrp SPB
 !
 address-family ipv4 unicast autonomous-system 20
  !
  af-interface Ethernet0/1
   summary-address 10.20.0.0 255.255.0.0
   no passive-interface
  exit-af-interface
```

Проверим тамблице маршрутизации на R18
```
R18#show ip route eigrp 
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       + - replicated route, % - next hop override

Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 6 subnets, 3 masks
D        10.20.0.0/16 [90/1024640] via 10.20.0.110, 00:38:22, Ethernet0/1
                      [90/1024640] via 10.20.0.102, 00:38:22, Ethernet0/0
      172.10.0.0/24 is subnetted, 2 subnets
D        172.10.80.0 [90/1541120] via 10.20.0.110, 00:38:22, Ethernet0/1
                     [90/1541120] via 10.20.0.102, 00:38:22, Ethernet0/0
D        172.10.100.0 [90/1541120] via 10.20.0.110, 00:38:22, Ethernet0/1
                      [90/1541120] via 10.20.0.102, 00:38:22, Ethernet0/0
```
