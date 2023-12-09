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


#### 3. Настройте офиса Москва так, чтобы приоритетным провайдером стал Ламас.
Увеличим значение local-preference на 150 на взодящий трафик с провайдера Ламас.   
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


#### 4. Настройте офиса С.-Петербург так, чтобы трафик до любого офиса распределялся по двум линкам одновременно.   
