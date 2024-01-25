# Лабороторная работа на тему:    
## Основные протоколы сети интернет

### Цель:
Настроить DHCP в офисе Москва
Настроить синхронизацию времени в офисе Москва
Настроить NAT в офисе Москва, C.-Перетбруг и Чокурдах   

### Описание/Пошаговая инструкция выполнения домашнего задания:
В этой самостоятельной работе мы ожидаем, что вы самостоятельно:   

1. Настроите NAT(PAT) на R14 и R15. Трансляция должна осуществляться в адрес автономной системы AS1001.  
2. Настроите NAT(PAT) на R18. Трансляция должна осуществляться в пул из 5 адресов автономной системы AS2042.  
3. Настроите статический NAT для R20.  
4. Настроите NAT так, чтобы R19 был доступен с любого узла для удаленного управления.  
   5*. Настроите статический NAT(PAT) для офиса Чокурдах.  
5. Настроите для IPv4 DHCP сервер в офисе Москва на маршрутизаторах R12 и R13. VPC1 и VPC7 должны получать сетевые настройки по DHCP.  
6. Настроите NTP сервер на R12 и R13. Все устройства в офисе Москва должны синхронизировать время с R12 и R13.  



#### 1. Настроите NAT(PAT) на R14 и R15. Трансляция должна осуществляться в адрес автономной системы AS1001.  
Создадим access-list, с пользовательскими сетями 
```
R14(config)#access-list 1 permit 172.10.10.0 0.0.0.255
R14(config)#access-list 7 permit 172.10.70.0 0.0.0.255
```
Создадим Loopback интерфейсы с адресами, которые будем натить
```
R14(config-if)#ip address 10.10.10.11 255.255.255.255
R14(config-if)#ip nat inside 
Jan 25 09:10:05.624: %LINEPROTO-5-UPDOWN: Line protocol on Interface NVI0, changed state to up
R14(config-if)#int lo71                              
Jan 25 09:10:23.153: %LINEPROTO-5-UPDOWN: Line protocol on Interface Loopback71, changed state to up
R14(config-if)#ip address 10.10.10.71 255.255.255.255
R14(config-if)#ip nat inside                         
```
Добавим правило NAT
```
R14(config)#ip nat inside source list 1 interface Loopback11 overload
R14(config)#ip nat inside source list 7 interface Loopback71 overload
```

Добавим в анонс bgp Loopback адреса 
```
R14(config)#router bgp 1001
R14(config-router)#address-family ipv4
R14(config-router-af)#network 10.10.10.11 mask 255.255.255.255
R14(config-router-af)#network 10.10.10.71 mask 255.255.255.255
```

Пропишем на интерфейсе в сторону провайдера, что он является внешним
```
R14(config)#int e0/2
R14(config-if)#ip nat outside 
```

На R15 настройки аналогичны, только адреса 
```
10.10.10.10
10.10.10.70
```

Проверим доступность офиса СПБ с vpc в офисе Москва
```
VPC#ping 10.20.0.4
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.20.0.4, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 3/4/6 ms
VPC#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
Ethernet0/0                172.10.10.1     YES DHCP   up                    up      
Ethernet0/1                unassigned      YES NVRAM  administratively down down    
Ethernet0/2                unassigned      YES NVRAM  administratively down down    
Ethernet0/3                unassigned      YES NVRAM  administratively down down    

```
Посмотрим таблицу NAT трансляции
```
R15#show ip nat translations 
Pro Inside global      Inside local       Outside local      Outside global
--- 10.10.10.5         10.10.0.5          ---                ---
icmp 10.10.10.10:7     172.10.10.1:7      10.20.0.4:7        10.20.0.4:7
```

#### 2. Настроите NAT(PAT) на R18. Трансляция должна осуществляться в пул из 5 адресов автономной системы AS2042.  
Настройка аналогична Москве, только сейчас мы указываем pool
```
R18(config)#access-list 1 permit 172.10.80.0 0.0.0.255
R18(config)#access-list 1 permit 172.10.100.0 0.0.0.255

R18(config)#ip route 10.20.20.0 255.255.255.248 null 0
R18(config)#ip nat pool NAT-SPB 10.20.20.1 10.20.20.5 netmask 255.255.255.248
R18(config)#ip nat inside source list 1 pool NAT-SPB overload

R18(config)#int range e0/2-3
R18(config-if-range)#ip nat outside 
R18(config-if-range)#int range e0/0-1     
R18(config-if-range)#ip nat inside

R18(config)#router bgp 2042
R18(config-router)#address-family ipv4
R18(config-router-af)#network 10.20.20.0 mask 255.255.255.248
R18(config-router-af)#no network 10.20.0.0 mask 255.255.0.0
```

Так как у нас настроена фильтация в сторону провайдера, добавим сеть в prefix-list
```
R18(config)#ip prefix-list Triada-out seq 5 permit 10.20.20.0/29 
```
Проверим
```
VPCS> show

NAME   IP/MASK              GATEWAY                             GATEWAY
VPCS1  172.10.80.1/24       172.10.80.254
       fe80::250:79ff:fe66:6808/64
       2001:bbbb::80:1/122 
VPCS> ping 10.10.10.100

84 bytes from 10.10.10.100 icmp_seq=1 ttl=250 time=3.816 ms
84 bytes from 10.10.10.100 icmp_seq=2 ttl=250 time=3.431 ms
84 bytes from 10.10.10.100 icmp_seq=3 ttl=250 time=4.082 ms
84 bytes from 10.10.10.100 icmp_seq=4 ttl=250 time=3.617 ms
84 bytes from 10.10.10.100 icmp_seq=5 ttl=250 time=3.490 ms

VPCS> ping 10.30.0.1  

84 bytes from 10.30.0.1 icmp_seq=1 ttl=250 time=4.253 ms
84 bytes from 10.30.0.1 icmp_seq=2 ttl=250 time=3.489 ms
84 bytes from 10.30.0.1 icmp_seq=3 ttl=250 time=3.072 ms
84 bytes from 10.30.0.1 icmp_seq=4 ttl=250 time=3.123 ms
84 bytes from 10.30.0.1 icmp_seq=5 ttl=250 time=2.825 ms
```
Посмотрим таблицу nat 
```
R18#show ip nat translations 
Pro Inside global      Inside local       Outside local      Outside global
icmp 10.20.20.1:45874  172.10.80.1:45874  10.10.10.100:45874 10.10.10.100:45874
icmp 10.20.20.1:46130  172.10.80.1:46130  10.10.10.100:46130 10.10.10.100:46130
icmp 10.20.20.1:46386  172.10.80.1:46386  10.10.10.100:46386 10.10.10.100:46386
icmp 10.20.20.1:46642  172.10.80.1:46642  10.10.10.100:46642 10.10.10.100:46642
icmp 10.20.20.1:46898  172.10.80.1:46898  10.10.10.100:46898 10.10.10.100:46898
icmp 10.20.20.1:47922  172.10.80.1:47922  10.30.0.1:47922    10.30.0.1:47922
icmp 10.20.20.1:48178  172.10.80.1:48178  10.30.0.1:48178    10.30.0.1:48178
icmp 10.20.20.1:48434  172.10.80.1:48434  10.30.0.1:48434    10.30.0.1:48434
icmp 10.20.20.1:48690  172.10.80.1:48690  10.30.0.1:48690    10.30.0.1:48690
icmp 10.20.20.1:48946  172.10.80.1:48946  10.30.0.1:48946    10.30.0.1:48946

```
##### 3. Настроите статический NAT для R20.  
Добавис статический нат 
```
ip nat inside source static 10.10.0.6 10.10.10.20
```
Добавим анонс в BGP и статический маршрут
```
router bgp 1001
 address-family ipv4
  network 10.10.10.20 mask 255.255.255.255

ip route 10.10.10.20 255.255.255.255 null0
```
Проверим
```
R20#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
Ethernet0/0                10.10.0.119     YES NVRAM  up                    up      
Ethernet0/1                unassigned      YES NVRAM  administratively down down    
Ethernet0/2                unassigned      YES NVRAM  administratively down down    
Ethernet0/3                unassigned      YES NVRAM  administratively down down    
Loopback0                  10.10.0.6       YES NVRAM  up                    up

R20#ping 10.30.0.1 source lo0 
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.30.0.1, timeout is 2 seconds:
Packet sent with a source address of 10.10.0.6 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 3/3/4 ms
```
Посмотрим таблицу NAT
```
R15#show ip nat translations 
Pro Inside global      Inside local       Outside local      Outside global
--- 10.10.10.5         10.10.0.5          ---                ---
icmp 10.10.10.20:6     10.10.0.6:6        10.30.0.1:6        10.30.0.1:6
--- 10.10.10.20        10.10.0.6          ---                ---
```

##### 4. Настроите NAT так, чтобы R19 был доступен с любого узла для удаленного управления.  
####     5*. Настроите статический NAT(PAT) для офиса Чокурдах.  



##### 5. Настроите для IPv4 DHCP сервер в офисе Москва на маршрутизаторах R12 и R13. VPC1 и VPC7 должны получать сетевые настройки по DHCP.  



##### 6. Настроите NTP сервер на R12 и R13. Все устройства в офисе Москва должны синхронизировать время с R12 и R13.  
