# Лабороторная работа на тему:    
## IS-IS

![Alt text](https://github.com/bislogin/otus/blob/main/labs/lab06/topology.png)

### Цель:
Настроить IS-IS офисе Триада

### Описание/Пошаговая инструкция выполнения домашнего задания:
1. Настроите IS-IS в ISP Триада.
2. R23 и R25 находятся в зоне 2222.
3. R24 находится в зоне 24.
4. R26 находится в зоне 26.  

Минимальные настройки:

```
R23(config)#router isis
R23(config-router)#net 49.2222.0100.4000.0001.00
```
где 49 - указывает тип адреса (у нас – приватный), 2222 - номер зоны, 0010.4000.0001 - ID устройства, 00 - селектор (всегда ноль).

На интерфейсах выполнить:
```
(config-if)#ip router isis
(config-if)#ipv6 router isis
```

Выполняем на R23, R24, R25, R26 минимальные настройки, указываем свою зону и ID, проверяем.

Соседство:
```
R23#show isis neighbors 

System Id      Type Interface   IP Address      State Holdtime Circuit Id
R24            L2   Et0/2       10.40.0.103     UP    28       01
R25            L1   Et0/1       10.40.0.101     UP    7        R25.01             
R25            L2   Et0/1       10.40.0.101     UP    8        R25.01
```
Таблицы маршрутизыции:
```
R23#show ip route isis
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       + - replicated route, % - next hop override

Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 10 subnets, 2 masks
i L2     10.40.0.2/32 [115/20] via 10.40.0.103, 05:11:05, Ethernet0/2
i L1     10.40.0.3/32 [115/20] via 10.40.0.101, 05:09:55, Ethernet0/1
i L2     10.40.0.4/32 [115/30] via 10.40.0.103, 05:09:53, Ethernet0/2
                      [115/30] via 10.40.0.101, 05:09:53, Ethernet0/1
i L2     10.40.0.104/31 [115/20] via 10.40.0.103, 05:11:05, Ethernet0/2
i L1     10.40.0.106/31 [115/20] via 10.40.0.101, 05:09:55, Ethernet0/1
```
```
R23#show ipv6 route isis
IPv6 Routing Table - default - 13 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
       B - BGP, HA - Home Agent, MR - Mobile Router, R - RIP
       H - NHRP, I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea
       IS - ISIS summary, D - EIGRP, EX - EIGRP external, NM - NEMO
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF Intra, OI - OSPF Inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2, l - LISP
I2  2001:DDDD::2/128 [115/20]
     via FE80::67, Ethernet0/2
I1  2001:DDDD::3/128 [115/20]
     via FE80::65, Ethernet0/1
I2  2001:DDDD::4/128 [115/30]
     via FE80::67, Ethernet0/2
     via FE80::65, Ethernet0/1
I1  2001:DDDD::5A/127 [115/20]
     via FE80::65, Ethernet0/1
I2  2001:DDDD::68/127 [115/20]
     via FE80::67, Ethernet0/2
```

