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
Добавим статический нат 
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
На R15
```
ip nat inside source static 10.10.0.5 10.10.10.5
```
Добавим анонс в BGP и статический маршрут
```
R15(config)#router bgp 1001
R15(config-router)#address-family ipv4
R15(config-router-af)#network 10.10.10.5 mask 255.255.255.255

R15(config)#ip route 10.10.10.5 255.255.255.255 null0
```
На R19
```
line vty 0 4
 password 7 121A0C041104
 logging synchronous
 login
 transport input all
```
Проверим с SW9 в офисе СПБ
```
SW9#telnet 10.10.10.5 /source-interface vlan80
Trying 10.10.10.5 ... Open
This is a secure system. Authorized Access Only!

User Access Verification

Password: 
Password: 
R19>en
Password: 
R19#
R19#exit

[Connection to 10.10.10.5 closed by foreign host]
SW9#
```
Посмотрим на таблицу NAT на R15
```
R15#show ip nat translations 
Pro Inside global      Inside local       Outside local      Outside global
tcp 10.10.10.5:23      10.10.0.5:23       10.20.0.109:43052  10.20.0.109:43052
--- 10.10.10.5         10.10.0.5          ---                ---
--- 10.10.10.20        10.10.0.6          ---                ---
R15#
```
####     5*. Настроите статический NAT(PAT) для офиса Чокурдах.  
```
R28(config)#int lo30
R28(config-if)#ip address 10.30.30.30 255.255.255.255
R28(config-if)#ip nat inside 
R28(config-if)#int e0/2
R28(config-if)#ip nat inside 
R28(config-if)#int ran e0/0-1
R28(config-if-range)#ip nat outside 
R28(config-if-range)#exit
R28(config)#
R28(config)#ip nat inside source list 30 interface lo30 overload 
R28(config)#ip nat inside source list 31 interface lo30 overload 
```
```
Checking for duplicate address...
VPCS : 172.10.30.1 255.255.255.0 gateway 172.10.30.254

PC1 : 2001:cccc:1::30:1/112 

VPCS> ping 10.40.0.1

84 bytes from 10.40.0.1 icmp_seq=1 ttl=252 time=4.047 ms
84 bytes from 10.40.0.1 icmp_seq=2 ttl=252 time=3.624 ms
84 bytes from 10.40.0.1 icmp_seq=3 ttl=252 time=3.600 ms
84 bytes from 10.40.0.1 icmp_seq=4 ttl=252 time=3.310 ms
84 bytes from 10.40.0.1 icmp_seq=5 ttl=252 time=3.529 ms

VPCS>
```
```
R28#show ip nat translations 
Pro Inside global      Inside local       Outside local      Outside global
icmp 10.30.30.30:51771 172.10.30.1:51771  10.40.0.1:51771    10.40.0.1:51771
icmp 10.30.30.30:52027 172.10.30.1:52027  10.40.0.1:52027    10.40.0.1:52027
icmp 10.30.30.30:52283 172.10.30.1:52283  10.40.0.1:52283    10.40.0.1:52283
icmp 10.30.30.30:52539 172.10.30.1:52539  10.40.0.1:52539    10.40.0.1:52539
icmp 10.30.30.30:52795 172.10.30.1:52795  10.40.0.1:52795    10.40.0.1:52795
R28#
```

##### 5. Настроите для IPv4 DHCP сервер в офисе Москва на маршрутизаторах R12 и R13. VPC1 и VPC7 должны получать сетевые настройки по DHCP.  
В связи с особенностями построения сети, все настройки будут проводится на SW4 и SW5.   

Настройки на SW4   
Добавим исключения для пулов   
```
ip dhcp excluded-address 172.10.10.248 255.255.255.248
ip dhcp excluded-address 172.10.70.248 255.255.255.248
ip dhcp excluded-address 172.10.10.125 172.10.10.254
ip dhcp excluded-address 172.10.70.125 172.10.70.254
```
Создалим pool для пользовательских сетей
```
ip dhcp pool user
 network 172.10.10.0 255.255.255.0
 default-router 172.10.10.254 
ip dhcp pool user7
 network 172.10.70.0 255.255.255.0
 default-router 172.10.70.254 
ipv6 dhcp pool ipv6_user
 address prefix 2001:AAAA::10:0/112 lifetime 3600 10
ipv6 dhcp pool ipv6_user7
 address prefix 2001:AAAA::70:0/112 lifetime 3600 10
```

Для ipv6 на int vlan добавим настройки получения адреса по dhcp и нащначим пул
```
interface Vlan10
 ipv6 nd managed-config-flag
 ipv6 dhcp server ipv6_user
```
```
interface Vlan70
 ipv6 nd managed-config-flag
 ipv6 dhcp server ipv6_user7
```
Для проверки заменим VPC на роутер, и настроим получения адреса по DHCP
```
interface Ethernet0/0
 ip address dhcp
 ipv6 address dhcp
 ipv6 enable
```
```
VPC#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
Ethernet0/0                172.10.10.1     YES DHCP   up                    up      
Ethernet0/1                unassigned      YES NVRAM  administratively down down    
Ethernet0/2                unassigned      YES NVRAM  administratively down down    
Ethernet0/3                unassigned      YES NVRAM  administratively down down    
VPC#show ipv6 int br
Ethernet0/0            [up/up]
    FE80::A8BB:CCFF:FE00:1000
    2001:AAAA::10:3846
Ethernet0/1            [administratively down/down]
    unassigned
Ethernet0/2            [administratively down/down]
    unassigned
Ethernet0/3            [administratively down/down]
    unassigned
VPC#
```

Проверим, что адреса назначены в таблице dhcp binding
```
SW4(config)#do show ip dhcp binding 
Bindings from all pools not associated with VRF:
IP address      Client-ID/              Lease expiration        Type       State      Interface
                Hardware address/
                User name
172.10.10.1     0063.6973.636f.2d61.    Jan 26 2024 11:57 AM    Automatic  Active     Vlan10
                6162.622e.6363.3030.
                2e31.3030.302d.4574.
                302f.30
```
```
SW5(config)#do show ipv6 dhcp binding 
Client: FE80::A8BB:CCFF:FE00:1000 
  DUID: 00030001AABBCC001000
  IA NA: IA ID 0x00030001, T1 5, T2 8
    Address: 2001:AAAA::10:3846
            preferred lifetime 10, valid lifetime 3600
            expires at Jan 25 2024 12:59 PM (3597 seconds)
```
Видим, что ipv4 получен на sw4, а ipv6 адрес получен с sw5.   

##### 6. Настроите NTP сервер на R12 и R13. Все устройства в офисе Москва должны синхронизировать время с R12 и R13.  

Настройка для сервера ntp
R12
```
ntp source Loopback0
ntp master 5
ntp peer 10.10.0.2
```
R13
```
ntp source Loopback0
ntp master 5
ntp peer 10.10.0.1
```
Настройки для всех остальных
```
ntp source Loopback0
ntp update-calendar
ntp server 10.10.0.1
ntp server 10.10.0.2
```

Проверка на сервере
```
R12#show ntp status 
Clock is synchronized, stratum 5, reference is 127.127.1.1    
nominal freq is 250.0000 Hz, actual freq is 250.0000 Hz, precision is 2**10
ntp uptime is 6997100 (1/100 of seconds), resolution is 4000
reference time is E95CC025.27EF9E20 (14:10:29.156 MSK Thu Jan 25 2024)
clock offset is 0.0000 msec, root delay is 0.00 msec
root dispersion is 2.25 msec, peer dispersion is 1.20 msec
loopfilter state is 'CTRL' (Normal Controlled Loop), drift is 0.000000000 s/s
system poll interval is 16, last update was 5 sec ago.
R12#show ntp ass    
R12#show ntp associations 

  address         ref clock       st   when   poll reach  delay  offset   disp
*~127.127.1.1     .LOCL.           4      9     16   377  0.000   0.000  1.204
 ~10.10.0.2       127.127.1.1      5     40     64   376  1.000  -0.500  3.450
 * sys.peer, # selected, + candidate, - outlyer, x falseticker, ~ configured
R12#
```
Проверка на роутере
```
R15#show ntp status 
Clock is synchronized, stratum 6, reference is 10.10.0.1      
nominal freq is 250.0000 Hz, actual freq is 250.0000 Hz, precision is 2**10
ntp uptime is 7002200 (1/100 of seconds), resolution is 4000
reference time is E95CC04A.195810A8 (14:11:06.099 MSK Thu Jan 25 2024)
clock offset is 0.0000 msec, root delay is 0.00 msec
root dispersion is 5.55 msec, peer dispersion is 1.99 msec
loopfilter state is 'CTRL' (Normal Controlled Loop), drift is -0.000000001 s/s
system poll interval is 1024, last update was 24 sec ago.
R15#show ntp ass    
R15#show ntp associations 

  address         ref clock       st   when   poll reach  delay  offset   disp
*~10.10.0.1       127.127.1.1      5     30   1024   377  0.000   0.000  1.994
+~10.10.0.2       127.127.1.1      5    552   1024   377  0.000   0.000  2.013
 * sys.peer, # selected, + candidate, - outlyer, x falseticker, ~ configured
R15#
```
Проверка на коммутаторе
```
SW3#show ntp status 
Clock is synchronized, stratum 6, reference is 10.10.0.2      
nominal freq is 250.0000 Hz, actual freq is 250.0000 Hz, precision is 2**10
ntp uptime is 7032800 (1/100 of seconds), resolution is 4000
reference time is E95CC186.C872B248 (14:16:22.783 MSK Thu Jan 25 2024)
clock offset is 0.5000 msec, root delay is 2.00 msec
root dispersion is 7941.93 msec, peer dispersion is 189.47 msec
loopfilter state is 'CTRL' (Normal Controlled Loop), drift is 0.000000000 s/s
system poll interval is 64, last update was 11 sec ago.
SW3#show ntp ass    
SW3#show ntp associations 

  address         ref clock       st   when   poll reach  delay  offset   disp
 ~10.10.0.1       127.127.1.1      5      5     64     1  1.000   0.500 189.47
*~10.10.0.2       127.127.1.1      5      5     64     1  1.000   0.500 189.47
 * sys.peer, # selected, + candidate, - outlyer, x falseticker, ~ configured
```
