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

Создадим prefix-list с сетями кождого VPC

```
ip prefix-list NET30 seq 5 permit 172.10.30.0/24
ip prefix-list NET31 seq 5 permit 172.10.31.0/24
ipv6 prefix-list NET30v6 seq 5 permit 2001:CCCC:1::30:0/112
ipv6 prefix-list NET31v6 seq 5 permit 2001:CCCC:1::31:0/112
```

Создадим route-map, в котором заменим next-hop для кождого VPC

```
route-map BALANCE permit 10
 match ip address prefix-list NET30
 set ip next-hop 100.100.30.2

route-map BALANCE permit 20
 match ip address prefix-list NET31
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

Добавим статичесткие маршруты для офиса Лабытнанги

```
ip route 0.0.0.0 0.0.0.0 100.100.35.2 name "def v4 to Triada"
ipv6 route ::/0 2001:CCCC:2:CCDD::2 name "def v6 to Triada"
```
