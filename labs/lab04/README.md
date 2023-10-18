# Лабороторная работа на тему:  
## Маршрутизация на основе политик (PBR)

### Цель:
Настроить политику маршрутизации в офисе Чокурдах 
Распределить трафик между 2 линками 

#### Описание/Пошаговая инструкция выполнения домашнего задания:

 Настроить политику маршрутизации для сетей офиса.  
 Распределить трафик между двумя линками с провайдером.  
 Настроить отслеживание линка через технологию IP SLA.(только для IPv4)  
 Настройть для офиса Лабытнанги маршрут по-умолчанию.    


#### Решение:

Распределим трафик, VPC30 будет ходить через R26, а VPC31 через R25

Создадим access-list с сетями кождого VPC

```
ip access-list standard 30
 permit 172.10.30.0 0.0.0.255
ip access-list standard 31
 permit 172.10.31.0 0.0.0.255

```

Создадим route-map, в котором заменим next-hop для кождого VPC

```
route-map BALANCE permit 10
 match ip address prefix-list 30
 set ip next-hop 100.100.30.2

route-map BALANCE permit 20
 match ip address prefix-list 31
 set ip next-hop 100.100.31.2

route-map BALANCE deny 30
```

Настроим отслеживание линка через технологию IP SLA.

```
ip sla 25
 icmp-echo 100.100.31.2 source-interface Ethernet0/1
 timeout 15000
 frequency 15
ip sla schedule 25 life forever start-time now
ip sla 26
 icmp-echo 100.100.30.2 source-interface Ethernet0/0
 timeout 15000
 frequency 15
ip sla schedule 26 life forever start-time now
```

Определим объект, который отслеживает SLA.

```
track 25 ip sla 25 reachability
 delay down 5 up 5

track 26 ip sla 26 reachability
 delay down 5 up 5
```

Обновим route-map с отслеживанием IP SLA

```
route-map BALANCE permit 10
 match ip address prefix-list NET30 NET30v6
 set ip next-hop verify-availability 100.100.30.2 1 track 26
 set ip next-hop 100.100.30.2

route-map BALANCE permit 20
 match ip address prefix-list NET31 NET31v6
 set ip next-hop verify-availability 100.100.31.2 1 track 25
 set ip next-hop 100.100.31.2

route-map BALANCE deny 30
```
Добавляем route=map на интерфейс
```
interface Ethernet0/2
 description == link to SW29 ==
 ip address 10.30.0.100 255.255.255.254
 ip policy route-map BALANCE
 ipv6 address FE80::64 link-local
 ipv6 address 2001:CCCC:1::64/127
 ipv6 enable
```
Добавим статичесткие маршруты

```
ip route 0.0.0.0 0.0.0.0 100.100.31.2 254 name "track defaulte to R25" track 25
ip route 0.0.0.0 0.0.0.0 100.100.30.2 254 name "track defaulte to R26" track 26
ipv6 route ::/0 2001:CCCC:1:CCD2::2 254 name "default ipv6 to R25"
ipv6 route ::/0 2001:CCCC:1:CCD1::2 254 name "default ipv6 to R26"
```
Проверим как хходит трафик с VPC
```
VPCS> show ip

NAME        : VPCS[1]
IP/MASK     : 172.10.30.1/24
GATEWAY     : 172.10.30.254
DNS         : 
MAC         : 00:50:79:66:68:1e
LPORT       : 20000
RHOST:PORT  : 127.0.0.1:30000
MTU         : 1500

VPCS> trace 10.40.0.107
trace to 10.40.0.107, 8 hops max, press Ctrl+C to stop
 1   172.10.30.254   0.264 ms  0.261 ms  0.272 ms
 2   10.30.0.100   0.634 ms  0.531 ms  0.544 ms
 3   *100.100.30.2   0.822 ms (ICMP type:3, code:3, Destination port unreachable)  *

VPCS> 
```

```
VPCS> show ip

NAME        : VPCS[1]
IP/MASK     : 172.10.31.1/24
GATEWAY     : 172.10.31.254
DNS         : 
MAC         : 00:50:79:66:68:1f
LPORT       : 20000
RHOST:PORT  : 127.0.0.1:30000
MTU         : 1500

VPCS> trace 10.40.0.107
trace to 10.40.0.107, 8 hops max, press Ctrl+C to stop
 1   172.10.31.254   0.208 ms  0.250 ms  0.309 ms
 2   10.30.0.100   0.538 ms  0.438 ms  0.514 ms
 3   100.100.31.2   0.812 ms  0.705 ms  0.686 ms
 4   *10.40.0.107   0.942 ms (ICMP type:3, code:3, Destination port unreachable)  *

VPCS> 
```


Добавим статичесткие маршруты для офиса Лабытнанги

```
ip route 0.0.0.0 0.0.0.0 100.100.35.2 name "def v4 to Triada"
ipv6 route ::/0 2001:CCCC:2:CCDD::2 name "def v6 to Triada"
```
