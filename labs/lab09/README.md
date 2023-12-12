# Лабороторная работа на тему:    
## iBGP

### Цель:
Настроить iBGP в офисе Москва   
Настроить iBGP в сети провайдера Триада   
Организовать полную IP связанность всех сетей   

### Описание/Пошаговая инструкция выполнения домашнего задания:
В этой самостоятельной работе мы ожидаем, что вы самостоятельно:   

1. Настроите iBGP в офисе Москва между маршрутизаторами R14 и R15.
2. Настроите iBGP в провайдере Триада, с использованием RR.
3. Настройте офиса Москва так, чтобы приоритетным провайдером стал Ламас.
4. Настройте офиса С.-Петербург так, чтобы трафик до любого офиса распределялся по двум линкам одновременно.   


#### 1. Настроите iBGP в офисе Москва между маршрутизаторами R14 и R15.

Настроим соседство между R14 и R15   
Привет на R14:
```
router bgp 1001
 neighbor 10.10.0.125 remote-as 1001
 neighbor 10.10.0.125 description == BGP to R15
 neighbor 2001:AAAA::7D remote-as 1001
 neighbor 2001:AAAA::7D description == IPV6 BGP to R15
 !
 address-family ipv4
  neighbor 10.10.0.125 activate
  no neighbor 2001:AAAA::7D activate
 exit-address-family
 !
 address-family ipv6
  neighbor 2001:AAAA::7D activate
 exit-address-family
```
Проверим соседа:
```
R14#show ip bgp summary 
BGP router identifier 10.10.0.3, local AS number 1001
BGP table version is 46, main routing table version 46
15 network entries using 2220 bytes of memory
31 path entries using 1984 bytes of memory
11/7 BGP path/bestpath attribute entries using 1496 bytes of memory
9 BGP AS-PATH entries using 216 bytes of memory
0 BGP route-map cache entries using 0 bytes of memory
0 BGP filter-list cache entries using 0 bytes of memory
BGP using 5916 total bytes of memory
BGP activity 40/10 prefixes, 87/27 paths, scan interval 60 secs

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.10.0.125     4         1001   34580   34605       46    0    0 3w0d           15
100.100.10.2    4          101   34668   34642       46    0    0 3w0d           14
```
```
R14#show bgp ipv6 unicast summary 
BGP router identifier 10.10.0.3, local AS number 1001
BGP table version is 63, main routing table version 63
15 network entries using 2580 bytes of memory
29 path entries using 2552 bytes of memory
11/6 BGP path/bestpath attribute entries using 1496 bytes of memory
9 BGP AS-PATH entries using 216 bytes of memory
0 BGP route-map cache entries using 0 bytes of memory
0 BGP filter-list cache entries using 0 bytes of memory
BGP using 6844 total bytes of memory
BGP activity 40/10 prefixes, 87/27 paths, scan interval 60 secs

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
2001:AAAA::7D   4         1001   34516   34506       63    0    0 3w0d           14
2001:AAAA:AAFF::2
                4          101   34489   34516       63    0    0 3w0d           13
```

#### 2. Настроите iBGP в провайдере Триада, с использованием RR.
Назначим в качестве RR роутер R24   
Добавим iBGP соседство между R24 и R25 через интерфейсы lo0
```
router bgp 520
 neighbor 10.40.0.3 remote-as 520
 neighbor 10.40.0.3 description == BGP to R25
 neighbor 10.40.0.3 update-source Loopback0
 neighbor 2001:DDDD::3 remote-as 520
 neighbor 2001:DDDD::3 description == IPV6 BGP to R25
 neighbor 2001:DDDD::3 update-source Loopback0
```
Обозначим на R24 соседей, которые будут route-reflector-client
```
router bgp 520
 address-family ipv4
  neighbor 10.40.0.3 activate
  neighbor 10.40.0.3 route-reflector-client
  neighbor 10.40.0.102 activate
  neighbor 10.40.0.102 route-reflector-client
  neighbor 10.40.0.105 activate
  neighbor 10.40.0.105 route-reflector-client
 address-family ipv6
  neighbor 2001:DDDD::3 activate
  neighbor 2001:DDDD::3 route-reflector-client
  neighbor 2001:DDDD::66 activate
  neighbor 2001:DDDD::66 route-reflector-client
  neighbor 2001:DDDD::69 activate
  neighbor 2001:DDDD::69 route-reflector-client
```
Погасим все iBGP соседства на R25 кроме RR
```
R25#sh ip bgp summary
BGP router identifier 10.40.0.3, local AS number 520
BGP table version is 40, main routing table version 40
15 network entries using 2160 bytes of memory
16 path entries using 1344 bytes of memory
6/6 BGP path/bestpath attribute entries using 960 bytes of memory
2 BGP rrinfo entries using 48 bytes of memory
5 BGP AS-PATH entries using 120 bytes of memory
0 BGP route-map cache entries using 0 bytes of memory
0 BGP filter-list cache entries using 0 bytes of memory
BGP using 4632 total bytes of memory
BGP activity 33/3 prefixes, 71/29 paths, scan interval 60 secs

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.40.0.2       4          520      36      28       40    0    0 00:20:37       13
10.40.0.100     4          520       0       0        1    0    0 never    Idle (Admin)
10.40.0.107     4          520       0       0        1    0    0 never    Idle (Admin)
```
Посмотрим таблицу BGP
```
R25#show ip bgp 
BGP table version is 40, local router ID is 10.40.0.3
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
              t secondary path, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>i  10.10.0.0/16     100.100.40.6             0    100      0 301 1001 i
 *>i  10.20.0.0/16     100.100.21.1             0    100      0 2042 i
 * i  10.40.0.0/16     10.40.0.2                0    100      0 i
 *>                    0.0.0.0                  0         32768 i
 *>i  10.50.0.0/16     100.100.40.6             0    100      0 301 i
 *>i  10.60.0.0/16     100.100.40.2             0    100      0 101 i
 *>i  100.100.10.0/30  100.100.40.2             0    100      0 101 i
 *>i  100.100.11.0/30  100.100.40.6             0    100      0 301 i
 *>i  100.100.21.0/30  10.40.0.2                0    100      0 i
 *>i  100.100.22.0/30  10.40.0.105              0    100      0 i
 *>i  100.100.30.0/30  10.40.0.105              0    100      0 i
 *>   100.100.31.0/30  0.0.0.0                  0         32768 i
 *>   100.100.35.0/30  0.0.0.0                  0         32768 i
 *>i  100.100.40.0/30  10.40.0.102              0    100      0 i
 *>i  100.100.40.4/30  10.40.0.2                0    100      0 i
 *>i  100.100.50.0/30  100.100.40.6             0    100      0 301 i
```
Видим, что мы получаем от RR все маршруты, от AS 101,301,1001 и 2042.

#### 3. Настройте офиса Москва так, чтобы приоритетным провайдером стал Ламас.
Увеличим значение local-preference на 150 на входящий трафик с провайдера Ламас.   
Создадим route-map:
```
R15(config)#route-map LP150 permit 10 
R15(config-route-map)#set local-preference 150
R15(config-route-map)#exit
```
Применим его к соседу:
```
R15(config)#router bgp 1001
R15(config-router)#address-family ipv4
R15(config-router-af)#neighbor 100.100.11.2 route-map LP150 in
R15(config-router)#address-family ipv6
R15(config-router-af)#neighbor 2001:AAAA:AAEE::2 route-map LP150 in 
R15(config-router-af)#end
```
Между R14 и R15 добавим next-hop-self:
```
router bgp 1001
 address-family ipv4
  neighbor 10.10.0.124 next-hop-self
 address-family ipv6
  neighbor 2001:AAAA::7C next-hop-self
```

Проверим, что значение изменилось:
```
R15#  show ip bgp 
BGP table version is 63, local router ID is 10.10.0.4
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  10.10.0.0/16     0.0.0.0                  0         32768 i
 * i                  10.10.0.124              0    100      0 i
 * i 10.20.0.0/16     100.100.10.2             0    100      0 101 520 2042 i
 *>                   100.100.11.2                  150      0 301 520 2042 i
 *>  10.40.0.0/16     100.100.11.2                  150      0 301 520 i
 * i                  100.100.10.2             0    100      0 101 520 i
 * i 10.50.0.0/16     100.100.10.2             0    100      0 101 301 i
 *>                   100.100.11.2             0    150      0 301 i
 *>  10.60.0.0/16     100.100.11.2                  150      0 301 101 i
 * i                  100.100.10.2             0    100      0 101 i
 *>  100.100.10.0/30  100.100.11.2                  150      0 301 101 i
 * i                  10.10.0.124              0    100      0 i
 r>  100.100.11.0/30  100.100.11.2             0    150      0 301 i
 * i 100.100.21.0/30  100.100.10.2             0    100      0 101 520 i
     Network          Next Hop            Metric LocPrf Weight Path
 *>                   100.100.11.2                  150      0 301 520 i
 * i 100.100.22.0/30  100.100.10.2             0    100      0 101 301 520 i
 *>                   100.100.11.2                  150      0 301 520 i
 * i 100.100.30.0/30  100.100.10.2             0    100      0 101 301 520 i
 *>                   100.100.11.2                  150      0 301 520 i
 *>  100.100.31.0/30  100.100.11.2                  150      0 301 520 i
 * i                  100.100.10.2             0    100      0 101 301 520 i
 *>  100.100.35.0/30  100.100.11.2                  150      0 301 520 i
 * i                  100.100.10.2             0    100      0 101 301 520 i
 *>  100.100.40.0/30  100.100.11.2                  150      0 301 520 i
 * i                  100.100.10.2             0    100      0 101 520 i
 * i 100.100.40.4/30  100.100.10.2             0    100      0 101 301 i
 *>                   100.100.11.2             0    150      0 301 i
 * i 100.100.50.0/30  100.100.10.2             0    100      0 101 301 i
 *>                   100.100.11.2             0    150      0 301 i
```
```
R15#show bgp ipv6 unicast               
BGP table version is 58, local router ID is 10.10.0.4
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 * i 2001:AAAA::/64   2001:AAAA::7C            0    100      0 i
 *>                   ::                       0         32768 i
 *   2001:AAAA:AAEE::/64
                       2001:AAAA:AAEE::2
                                                0    150      0 301 i
 *>                   ::                       0         32768 i
 *>i 2001:AAAA:AAFF::/64
                       2001:AAAA::7C            0    100      0 i
 *>  2001:BBBB::/64   2001:AAAA:AAEE::2
                                                     150      0 301 520 2042 i
 *>  2001:BBBB:BBD1::/64
                       2001:AAAA:AAEE::2
                                                     150      0 301 520 i
 *>  2001:BBBB:BBD2::/64
                       2001:AAAA:AAEE::2
     Network          Next Hop            Metric LocPrf Weight Path
                                                     150      0 301 520 i
 *>  2001:CCCC:1:CCD1::/64
                       2001:AAAA:AAEE::2
                                                     150      0 301 520 i
 *>  2001:CCCC:1:CCD2::/64
                       2001:AAAA:AAEE::2
                                                     150      0 301 520 i
 *>  2001:CCCC:2:CCDD::/64
                       2001:AAAA:AAEE::2
                                                     150      0 301 520 i
 *>  2001:DDDD::/64   2001:AAAA:AAEE::2
                                                     150      0 301 520 i
 *>  2001:DDDD:DDEE:1::/64
                       2001:AAAA:AAEE::2
                                                0    150      0 301 i
 *>  2001:DDDD:DDFF::/64
                       2001:AAAA:AAEE::2
                                                     150      0 301 101 i
 *>  2001:EEEE::/64   2001:AAAA:AAEE::2
                                                0    150      0 301 i
 *>  2001:EEEE:EEFF::/64
                       2001:AAAA:AAEE::2
     Network          Next Hop            Metric LocPrf Weight Path
                                                0    150      0 301 i
 *>  2001:FFFF::/64   2001:AAAA:AAEE::2
                                                     150      0 301 101 i
```
Проверим на R14 таблицу маршрутизации:
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
B        10.20.0.0/16 [200/0] via 10.10.0.125, 00:13:08
B        10.40.0.0/16 [200/0] via 10.10.0.125, 00:13:08
B        10.50.0.0/16 [200/0] via 10.10.0.125, 00:13:08
B        10.60.0.0/16 [200/0] via 10.10.0.125, 00:13:08
      100.0.0.0/8 is variably subnetted, 11 subnets, 2 masks
B        100.100.11.0/30 [200/0] via 10.10.0.125, 00:13:08
B        100.100.21.0/30 [200/0] via 10.10.0.125, 00:13:08
B        100.100.22.0/30 [200/0] via 10.10.0.125, 00:13:08
B        100.100.30.0/30 [200/0] via 10.10.0.125, 00:13:08
B        100.100.31.0/30 [200/0] via 10.10.0.125, 00:13:08
B        100.100.35.0/30 [200/0] via 10.10.0.125, 00:13:08
B        100.100.40.0/30 [200/0] via 10.10.0.125, 00:13:08
B        100.100.40.4/30 [200/0] via 10.10.0.125, 00:13:08
B        100.100.50.0/30 [200/0] via 10.10.0.125, 00:13:08
```
Видим, все внешние маршруты идут от R15.

#### 4. Настройте офиса С.-Петербург так, чтобы трафик до любого офиса распределялся по двум линкам одновременно.   

Для балансировки по 2 линкам, добавим "maximum-paths 2" в настройках BGP
```
router bgp 2042
 address-family ipv4
  maximum-paths 2
 exit-address-family
 !
 address-family ipv6
  maximum-paths 2
 exit-address-family
```
Проверим таблицу BGP:
```
R18# show bgp 
BGP table version is 44, local router ID is 10.20.0.3
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *m  10.10.0.0/16     100.100.22.2                           0 520 301 1001 i
 *>                   100.100.21.2                           0 520 301 1001 i
 *>  10.20.0.0/16     0.0.0.0                  0         32768 i
 *m  10.40.0.0/16     100.100.22.2             0             0 520 i
 *>                   100.100.21.2             0             0 520 i
 *m  10.50.0.0/16     100.100.22.2                           0 520 301 i
 *>                   100.100.21.2                           0 520 301 i
 *m  10.60.0.0/16     100.100.22.2                           0 520 101 i
 *>                   100.100.21.2                           0 520 101 i
 *m  100.100.10.0/30  100.100.22.2                           0 520 101 i
 *>                   100.100.21.2                           0 520 101 i
 *m  100.100.11.0/30  100.100.22.2                           0 520 301 i
 *>                   100.100.21.2                           0 520 301 i
 *   100.100.21.0/30  100.100.22.2                           0 520 i
     Network          Next Hop            Metric LocPrf Weight Path
 *                    100.100.21.2             0             0 520 i
 *>                   0.0.0.0                  0         32768 i
 *   100.100.22.0/30  100.100.21.2                           0 520 i
 *                    100.100.22.2             0             0 520 i
 *>                   0.0.0.0                  0         32768 i
 *m  100.100.30.0/30  100.100.21.2                           0 520 i
 *>                   100.100.22.2             0             0 520 i
 *m  100.100.31.0/30  100.100.22.2                           0 520 i
 *>                   100.100.21.2                           0 520 i
 *m  100.100.35.0/30  100.100.22.2                           0 520 i
 *>                   100.100.21.2                           0 520 i
 *m  100.100.40.0/30  100.100.22.2                           0 520 i
 *>                   100.100.21.2                           0 520 i
 *m  100.100.40.4/30  100.100.22.2                           0 520 i
 *>                   100.100.21.2             0             0 520 i
 *m  100.100.50.0/30  100.100.22.2                           0 520 301 i
 *>                   100.100.21.2                           0 520 301 i
```
Так же таблицу маршрутизации:
```
R18#show ip route BGP
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       + - replicated route, % - next hop override

Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 10 subnets, 3 masks
B        10.10.0.0/16 [20/0] via 100.100.22.2, 01:01:43
                      [20/0] via 100.100.21.2, 01:01:43
B        10.40.0.0/16 [20/0] via 100.100.22.2, 01:01:43
                      [20/0] via 100.100.21.2, 01:01:43
B        10.50.0.0/16 [20/0] via 100.100.22.2, 01:01:43
                      [20/0] via 100.100.21.2, 01:01:43
B        10.60.0.0/16 [20/0] via 100.100.22.2, 00:56:39
                      [20/0] via 100.100.21.2, 00:56:39
      100.0.0.0/8 is variably subnetted, 12 subnets, 2 masks
B        100.100.10.0/30 [20/0] via 100.100.22.2, 00:56:39
                         [20/0] via 100.100.21.2, 00:56:39
B        100.100.11.0/30 [20/0] via 100.100.22.2, 01:01:43
                         [20/0] via 100.100.21.2, 01:01:43
B        100.100.30.0/30 [20/0] via 100.100.22.2, 01:01:22
                         [20/0] via 100.100.21.2, 01:01:22
B        100.100.31.0/30 [20/0] via 100.100.22.2, 00:47:28
                         [20/0] via 100.100.21.2, 00:47:28
B        100.100.35.0/30 [20/0] via 100.100.22.2, 00:47:28
                         [20/0] via 100.100.21.2, 00:47:28
B        100.100.40.0/30 [20/0] via 100.100.22.2, 00:56:39
                         [20/0] via 100.100.21.2, 00:56:39
B        100.100.40.4/30 [20/0] via 100.100.22.2, 01:01:43
                         [20/0] via 100.100.21.2, 01:01:43
B        100.100.50.0/30 [20/0] via 100.100.22.2, 01:01:43
                         [20/0] via 100.100.21.2, 01:01:43
```
```
R18#show ipv6 route BGP
IPv6 Routing Table - default - 23 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
       B - BGP, HA - Home Agent, MR - Mobile Router, R - RIP
       H - NHRP, I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea
       IS - ISIS summary, D - EIGRP, EX - EIGRP external, NM - NEMO
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF Intra, OI - OSPF Inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2, l - LISP
B   2001:AAAA::/64 [20/0]
     via FE80::DB2, Ethernet0/2
     via FE80::DB2, Ethernet0/3
B   2001:AAAA:AAEE::/64 [20/0]
     via FE80::DB2, Ethernet0/2
     via FE80::DB2, Ethernet0/3
B   2001:AAAA:AAFF::/64 [20/0]
     via FE80::DB2, Ethernet0/2
     via FE80::DB2, Ethernet0/3
B   2001:CCCC:1:CCD1::/64 [20/0]
     via FE80::DB2, Ethernet0/3
     via FE80::DB2, Ethernet0/2
B   2001:CCCC:1:CCD2::/64 [20/0]
     via FE80::DB2, Ethernet0/3
     via FE80::DB2, Ethernet0/2
B   2001:CCCC:2:CCDD::/64 [20/0]
     via FE80::DB2, Ethernet0/3
     via FE80::DB2, Ethernet0/2
B   2001:DDDD::/64 [20/0]
     via FE80::DB2, Ethernet0/2
     via FE80::DB2, Ethernet0/3
B   2001:DDDD:DDEE:1::/64 [20/0]
     via FE80::DB2, Ethernet0/2
     via FE80::DB2, Ethernet0/3
B   2001:DDDD:DDFF::/64 [20/0]
     via FE80::DB2, Ethernet0/2
     via FE80::DB2, Ethernet0/3
B   2001:EEEE::/64 [20/0]
     via FE80::DB2, Ethernet0/2
     via FE80::DB2, Ethernet0/3
B   2001:EEEE:EEFF::/64 [20/0]
     via FE80::DB2, Ethernet0/2
     via FE80::DB2, Ethernet0/3
B   2001:FFFF::/64 [20/0]
     via FE80::DB2, Ethernet0/2
     via FE80::DB2, Ethernet0/3
```

Видим, что каждый префикс доступен через два интерфейса.
