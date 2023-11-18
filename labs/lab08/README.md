# Лабороторная работа на тему:    
## BGP. Основы

### Цель:
Настроить BGP между автономными системами   
Организовать доступность между офисами Москва и С.-Петербург   

### Описание/Пошаговая инструкция выполнения домашнего задания:
В этой самостоятельной работе мы ожидаем, что вы самостоятельно:  

1. Настроить eBGP между офисом Москва и двумя провайдерами - Киторн и Ламас.
2. Настроить eBGP между провайдерами Киторн и Ламас.
3. Настроить eBGP между Ламас и Триада.
4. Настроить eBGP между офисом С.-Петербург и провайдером Триада.
5. Организовать IP доступность между пограничным роутерами офисами Москва и С.-Петербург.


Базовые настройки для всех роутеров    
Настроки на примере R14.       
Создаем процесс bgp и указываем router-id   
```
router bgp 1001
 bgp router-id 10.10.0.3
```

Определяем соседей
```
 neighbor 10.10.0.125 remote-as 1001
 neighbor 10.10.0.125 description == BGP to R15
 neighbor 2001:AAAA::7D remote-as 1001
 neighbor 2001:AAAA::7D description == IPV6 BGP to R15
 neighbor 2001:AAAA:AAFF::2 remote-as 101
 neighbor 100.100.10.2 remote-as 101
 neighbor 100.100.10.2 description == BGP to ISP Kitorn
```

Объявляем нужные сети, с использованием address-family
```
 address-family ipv4
  network 10.10.0.0 mask 255.255.0.0
  network 100.100.10.0 mask 255.255.255.252
  neighbor 10.10.0.125 activate
  no neighbor 2001:AAAA::7D activate
  no neighbor 2001:AAAA:AAFF::2 activate
  neighbor 100.100.10.2 activate
 exit-address-family
 !
 address-family ipv6
  network 2001:AAAA::/64
  network 2001:AAAA:AAFF::/64
  neighbor 2001:AAAA::7D activate
  neighbor 2001:AAAA:AAFF::2 activate
 exit-address-family
```

Так как мы объявляем сумарный маршрут, он должен быть в таблице маршрутизвции
```
ip route 10.10.0.0 255.255.0.0 Null0
ipv6 route 2001:AAAA::/64 Null0
```

Проверим соседские отношения, и таблицу маршрутов BGP на R14
```
R14#sh ip bgp summary
BGP router identifier 10.10.0.3, local AS number 1001
BGP table version is 24, main routing table version 24
15 network entries using 2220 bytes of memory
27 path entries using 1728 bytes of memory
10/7 BGP path/bestpath attribute entries using 1360 bytes of memory
8 BGP AS-PATH entries using 192 bytes of memory
0 BGP route-map cache entries using 0 bytes of memory
0 BGP filter-list cache entries using 0 bytes of memory
BGP using 5500 total bytes of memory
BGP activity 31/1 prefixes, 55/3 paths, scan interval 60 secs

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.10.0.125     4         1001    1199    1205       24    0    0 18:01:10       11
100.100.10.2    4          101    1282    1271       24    0    0 19:01:52       14


R14#show bgp ipv6 unicast summary
BGP router identifier 10.10.0.3, local AS number 1001
BGP table version is 21, main routing table version 21
15 network entries using 2580 bytes of memory
25 path entries using 2200 bytes of memory
10/7 BGP path/bestpath attribute entries using 1360 bytes of memory
8 BGP AS-PATH entries using 192 bytes of memory
0 BGP route-map cache entries using 0 bytes of memory
0 BGP filter-list cache entries using 0 bytes of memory
BGP using 6332 total bytes of memory
BGP activity 31/1 prefixes, 55/3 paths, scan interval 60 secs

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
2001:AAAA::7D   4         1001    1119    1120       21    0    0 16:50:15       10
2001:AAAA:AAFF::2
                4          101    1105    1107       21    0    0 16:36:10       13
```

```
R14#show ip route bgp
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       + - replicated route, % - next hop override

Gateway of last resort is 100.100.10.2 to network 0.0.0.0

      10.0.0.0/8 is variably subnetted, 30 subnets, 3 masks
B        10.20.0.0/16 [20/0] via 100.100.10.2, 17:12:25
B        10.40.0.0/16 [20/0] via 100.100.10.2, 17:32:43
B        10.50.0.0/16 [20/0] via 100.100.10.2, 17:45:34
B        10.60.0.0/16 [20/0] via 100.100.10.2, 17:46:04
      100.0.0.0/8 is variably subnetted, 11 subnets, 2 masks
B        100.100.11.0/30 [200/0] via 100.100.11.2, 17:59:16
B        100.100.21.0/30 [20/0] via 100.100.10.2, 17:24:39
B        100.100.22.0/30 [20/0] via 100.100.10.2, 17:16:47
B        100.100.30.0/30 [20/0] via 100.100.10.2, 17:17:18
B        100.100.31.0/30 [20/0] via 100.100.10.2, 17:21:17
B        100.100.35.0/30 [20/0] via 100.100.10.2, 17:20:46
B        100.100.40.0/30 [20/0] via 100.100.10.2, 17:32:43
B        100.100.40.4/30 [20/0] via 100.100.10.2, 17:59:11
B        100.100.50.0/30 [20/0] via 100.100.10.2, 17:59:11


R14#show ipv6 route bgp
IPv6 Routing Table - default - 47 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
       B - BGP, HA - Home Agent, MR - Mobile Router, R - RIP
       H - NHRP, I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea
       IS - ISIS summary, D - EIGRP, EX - EIGRP external, NM - NEMO
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF Intra, OI - OSPF Inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2, l - LISP
B   2001:AAAA:AAEE::/64 [200/0]
     via 2001:AAAA::7D
B   2001:BBBB::/64 [20/0]
     via FE80::FA, Ethernet0/2
B   2001:BBBB:BBD1::/64 [20/0]
     via FE80::FA, Ethernet0/2
B   2001:BBBB:BBD2::/64 [200/0]
     via 2001:AAAA:AAEE::2
B   2001:CCCC:1:CCD1::/64 [200/0]
     via 2001:AAAA:AAEE::2
B   2001:CCCC:1:CCD2::/64 [20/0]
     via FE80::FA, Ethernet0/2
B   2001:CCCC:2:CCDD::/64 [20/0]
     via FE80::FA, Ethernet0/2
B   2001:DDDD::/64 [20/0]
     via FE80::FA, Ethernet0/2
B   2001:DDDD:DDEE:1::/64 [200/0]
     via 2001:AAAA:AAEE::2
B   2001:DDDD:DDFF::/64 [20/0]
     via FE80::FA, Ethernet0/2
B   2001:EEEE::/64 [200/0]
     via 2001:AAAA:AAEE::2
B   2001:EEEE:EEFF::/64 [20/0]
     via FE80::FA, Ethernet0/2
B   2001:FFFF::/64 [20/0]
     via FE80::FA, Ethernet0/2
```

Проверим доступность роутера СПБ R18   

Ping c R14(Москва) до R18(Санкт-Петербург)
```
R14# ping 10.20.0.3 source lo0
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.20.0.3, timeout is 2 seconds:
Packet sent with a source address of 10.10.0.3 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms

R14#ping 2001:BBBB::3 source lo0
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:BBBB::3, timeout is 2 seconds:
Packet sent with a source address of 2001:AAAA::3
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/24/117 ms
```

Ping c R15(Москва) до R18(Санкт-Петербург)
```
R15#ping 10.20.0.3 source lo0
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.20.0.3, timeout is 2 seconds:
Packet sent with a source address of 10.10.0.4 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms

R15#ping 2001:BBBB::3 source lo0
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:BBBB::3, timeout is 2 seconds:
Packet sent with a source address of 2001:AAAA::4
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms
```
