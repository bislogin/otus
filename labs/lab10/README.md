# Лабороторная работа на тему:    
## BGP. Фильтрация

### Цель:
Настроить фильтрацию для офисе Москва   
Настроить фильтрацию для офисе С.-Петербург     

### Описание/Пошаговая инструкция выполнения домашнего задания:
В этой самостоятельной работе мы ожидаем, что вы самостоятельно:   

1. Настроить фильтрацию в офисе Москва так, чтобы не появилось транзитного трафика(As-path).
2. Настроить фильтрацию в офисе С.-Петербург так, чтобы не появилось транзитного трафика(Prefix-list).
3. Настроить провайдера Киторн так, чтобы в офис Москва отдавался только маршрут по умолчанию.
4. Настроить провайдера Ламас так, чтобы в офис Москва отдавался только маршрут по умолчанию и префикс офиса С.-Петербург.


#### 1. Настроить фильтрацию в офисе Москва так, чтобы не появилось транзитного трафика(As-path).
На роутерах R14 и R15 в Москве создадим as-path access-list с регулярным выражением, в котором укажем только нашу AS
```
ip as-path access-list 1 permit ^$
ip as-path access-list 1 deny .*
```
Создадим route-map, и применим в нем наш as-path access-list
```
route-map Kitorn-out permit 10
 match as-path 1
```
Применим route-map к соседу BGP в направлении out
```
router bgp 1001
 address-family ipv4
  neighbor 100.100.10.2 route-map Kitorn-out out
 exit-address-family
 !
 address-family ipv6
  neighbor 2001:AAAA:AAFF::2 route-map Kitorn-out out
```
Проверим, что мы отдаем провайдеру:
```
R14#  sh ip bgp neighbors 100.100.10.2 advertised-routes 
BGP table version is 130, local router ID is 10.10.0.3
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  10.10.0.0/16     0.0.0.0                  0         32768 i
 *>  100.100.10.0/30  0.0.0.0                  0         32768 i

```
```
R14# show bgp ipv6 unicast neighbors 2001:AAAA:AAFF::2 advertised-routes 
BGP table version is 108, local router ID is 10.10.0.3
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  2001:AAAA::/64   ::                       0         32768 i
 *>i 2001:AAAA:AAEE::/64
                       2001:AAAA::7D            0    100      0 i
 *>  2001:AAAA:AAFF::/64
                       ::                       0         32768 i

Total number of prefixes 3 
```
#### 2. Настроить фильтрацию в офисе С.-Петербург так, чтобы не появилось транзитного трафика(Prefix-list).
Создадим prefix-list, в котом укажем телько нашу суммарную сеть, чтобы не было транзитного трафика:
```
ip prefix-list Triada-out seq 10 permit 10.20.0.0/16
ip prefix-list Triada-out seq 100 deny 0.0.0.0/0 le 32
!
ipv6 prefix-list IPV6-Triada-out seq 10 permit 2001:BBBB::/64
ipv6 prefix-list IPV6-Triada-out seq 100 deny ::/0 le 128
```
Применим этот prefix-list к соседу BGP в направлении out:
```
router bgp 2042
 !
 address-family ipv4
  neighbor 100.100.21.2 prefix-list Triada-out out
  neighbor 100.100.22.2 prefix-list Triada-out out
 exit-address-family
 !
 address-family ipv6
  neighbor 2001:BBBB:BBD1::2 prefix-list IPV6-Triada-out out
  neighbor 2001:BBBB:BBD2::2 prefix-list IPV6-Triada-out out
 exit-address-family
```
Проверим, что мы отдаем провайдеру:
```
R18#  sh ip bgp neighbors 100.100.21.2 advertised-routes 
BGP table version is 60, local router ID is 10.20.0.3
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  10.20.0.0/16     0.0.0.0                  0         32768 i

Total number of prefixes 1 
R18#  sh ip bgp neighbors 100.100.22.2 advertised-routes 
BGP table version is 60, local router ID is 10.20.0.3
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  10.20.0.0/16     0.0.0.0                  0         32768 i

Total number of prefixes 1 
```
```
R18# show bgp ipv6 unicast neighbors 2001:BBBB:BBD1::2 advertised-routes 
BGP table version is 51, local router ID is 10.20.0.3
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  2001:BBBB::/64   ::                       0         32768 i

Total number of prefixes 1 
R18# show bgp ipv6 unicast neighbors 2001:BBBB:BBD2::2 advertised-routes 
BGP table version is 51, local router ID is 10.20.0.3
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  2001:BBBB::/64   ::                       0         32768 i

Total number of prefixes 1 
```

#### 3. Настроить провайдера Киторн так, чтобы в офис Москва отдавался только маршрут по умолчанию.
Создадим prefix-list, в котором разрешим передачу только маршрута по умолчанию:
```
ip prefix-list DEF seq 10 permit 0.0.0.0/0
ip prefix-list DEF seq 20 deny 0.0.0.0/0 le 32
ipv6 prefix-list IPV6-DEF seq 10 permit ::/0
ipv6 prefix-list IPV6-DEF seq 20 deny ::/0 le 128
```
Повесим его на соседа BGP и добавим передачу дефолтного маршрута:
```
router bgp 101
 !
 address-family ipv4
  neighbor 100.100.10.1 default-originate
  neighbor 100.100.10.1 prefix-list DEF out
 exit-address-family
 !
 address-family ipv6
  neighbor 2001:AAAA:AAFF::1 default-originate
  neighbor 2001:AAAA:AAFF::1 prefix-list IPV6-DEF out
 exit-address-family
```
Проверим, что мы отдаем в сторону Москвы:
```
R22#sh ip bgp neighbors 100.100.10.1 ad
BGP table version is 43, local router ID is 10.60.0.1
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

Originating default network 0.0.0.0

     Network          Next Hop            Metric LocPrf Weight Path

Total number of prefixes 0
R22# show bgp ipv6 unicast neighbors 2001:AAAA:AAFF::1 ad
BGP table version is 37, local router ID is 10.60.0.1
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

Originating default network ::/0

     Network          Next Hop            Metric LocPrf Weight Path

Total number of prefixes 0 
```
Посмотрим на R14 в Москве, что он получаем от провайдера:
```
R14# sh ip bgp neighbors 100.100.10.2 routes
BGP table version is 130, local router ID is 10.10.0.3
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 r   0.0.0.0          100.100.10.2                           0 101 i

Total number of prefixes 1 
```
```
R14#  show bgp ipv6 unicast neighbors 2001:AAAA:AAFF::2 routes
BGP table version is 108, local router ID is 10.10.0.3
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 r   ::/0             2001:AAAA:AAFF::2
                                                              0 101 i

Total number of prefixes 1 
```

#### 4. Настроить провайдера Ламас так, чтобы в офис Москва отдавался только маршрут по умолчанию и префикс офиса С.-Петербург.
Создадим prefix-list, в котором разрешим передачу маршрута по умолчанию и суммарного префикса от офиса С.-Петербург:
```
ip prefix-list MSK-out seq 10 permit 0.0.0.0/0
ip prefix-list MSK-out seq 20 permit 10.20.0.0/16
ip prefix-list MSK-out seq 100 deny 0.0.0.0/0 le 32
```
```
ipv6 prefix-list IPV6-MSK-out seq 10 permit ::/0
ipv6 prefix-list IPV6-MSK-out seq 20 permit 2001:BBBB::/64
ipv6 prefix-list IPV6-MSK-out seq 100 deny ::/0 le 128
```
Повесим его на соседа BGP и добавим передачу дефолтного маршрута:
```
router bgp 301
 !
 address-family ipv4
  neighbor 100.100.11.1 default-originate
  neighbor 100.100.11.1 prefix-list MSK-out out
 exit-address-family
 !
 address-family ipv6
  neighbor 2001:AAAA:AAEE::1 default-originate
  neighbor 2001:AAAA:AAEE::1 prefix-list IPV6-MSK-out out
 exit-address-family
```
Посмотрим, что мы передаем в сторону Москвы:
```
R21# sh ip bgp neighbors 100.100.11.1 ad    
BGP table version is 18, local router ID is 10.50.0.1
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

Originating default network 0.0.0.0

     Network          Next Hop            Metric LocPrf Weight Path
 *>  10.20.0.0/16     100.100.40.5                           0 520 2042 i

Total number of prefixes 1 
```
```
R21# show bgp ipv6 unicast neighbors 2001:AAAA:AAEE::1 ad    
BGP table version is 18, local router ID is 10.50.0.1
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

Originating default network ::/0

     Network          Next Hop            Metric LocPrf Weight Path
 *>  2001:BBBB::/64   2001:DDDD:DDEE:1::5
                                                              0 520 2042 i

Total number of prefixes 1 
```
Посмотрим на R15 в Москве, что он получаем от провайдера:
```
R15#  sh ip bgp neighbors 100.100.11.2 routes
BGP table version is 147, local router ID is 10.10.0.4
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 r>  0.0.0.0          100.100.11.2                  150      0 301 i
 *>  10.20.0.0/16     100.100.11.2                  150      0 301 520 2042 i

Total number of prefixes 2 
```
```
R15#  show bgp ipv6 unicast neighbors 2001:AAAA:AAEE::2 routes
BGP table version is 139, local router ID is 10.10.0.4
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  ::/0             2001:AAAA:AAEE::2
                                                     150      0 301 i
 *>  2001:BBBB::/64   2001:AAAA:AAEE::2
                                                     150      0 301 520 2042 i

Total number of prefixes 2 
```
