# Лабороторная работа на тему:    
## VPN. GRE. DmVPN

### Цель:
Настроить GRE между офисами Москва и С.-Петербург   
Настроить DMVPN между офисами Москва и Чокурдах, Лабытнанги   

### Описание/Пошаговая инструкция выполнения домашнего задания:
В этой самостоятельной работе мы ожидаем, что вы самостоятельно:   

1. Настроите GRE между офисами Москва и С.-Петербург.
2. Настроите DMVMN между Москва и Чокурдах, Лабытнанги.   
Все узлы в офисах в лабораторной работе должны иметь IP связность.


#### 1. Настроите GRE между офисами Москва и С.-Петербург.

Настройки на R15
```
interface Tunnel1020
 ip address 192.168.10.1 255.255.255.0
 ip mtu 1400
 ip tcp adjust-mss 1360
 tunnel source Loopback100
 tunnel destination 10.20.20.6
ip route 172.10.80.0 255.255.255.0 Tunnel1020
ip route 172.10.100.0 255.255.255.0 Tunnel1020
```
На R18
```
interface Tunnel1020
 ip address 192.168.10.2 255.255.255.0
 ip mtu 1400
 ip tcp adjust-mss 1360
 tunnel source Loopback18
 tunnel destination 10.10.10.100
ip route 172.10.10.0 255.255.255.0 Tunnel1020
ip route 172.10.70.0 255.255.255.0 Tunnel1020
```
Проверим c VPC1 доступность VPC8
```
VPC#show ip int br  
Interface                  IP-Address      OK? Method Status                Protocol
Ethernet0/0                172.10.10.1     YES DHCP   up                    up      
Ethernet0/1                unassigned      YES NVRAM  administratively down down    
Ethernet0/2                unassigned      YES NVRAM  administratively down down    
Ethernet0/3                unassigned      YES NVRAM  administratively down down    
VPC#ping 172.10.80.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 172.10.80.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 3/3/5 ms
VPC#traceroute 172.10.80.1
Type escape sequence to abort.
Tracing the route to 172.10.80.1
VRF info: (vrf in name/id, vrf out name/id)
  1 172.10.10.252 1 msec 1 msec 1 msec
  2 10.10.0.110 1 msec 2 msec 1 msec
  3 10.10.0.113 2 msec 2 msec 2 msec
  4 192.168.10.2 3 msec 4 msec 2 msec
  5 10.20.0.102 4 msec 6 msec 3 msec
  6 10.20.0.105 3 msec 4 msec 3 msec
  7 172.10.80.1 3 msec 4 msec 3 msec
VPC#
```
Видим, что на 4 хопе адрес нашего туннеля, значит трафик идет через него.


#### 2. Настроите DMVMN между Москва и Чокурдах, Лабытнанги.  

Настроим R15
```
interface Tunnel1030
 ip address 192.168.30.1 255.255.255.0
 no ip redirects
 ip mtu 1400
 ip nhrp map multicast dynamic
 ip nhrp network-id 100
 ip nhrp holdtime 300
 ip nhrp redirect
 ip tcp adjust-mss 1360
 tunnel source Loopback100
 tunnel mode gre multipoint

ip route 172.10.30.0 255.255.255.0 192.168.30.30
ip route 172.10.31.0 255.255.255.0 192.168.30.30
ip route 172.10.35.0 255.255.255.0 192.168.30.35
```

Настроим Лабытнаги R27
```
interface Tunnel1030
 ip address 192.168.30.35 255.255.255.0
 no ip redirects
 ip mtu 1400
 ip nhrp map multicast 10.10.10.100
 ip nhrp map 192.168.30.1 10.10.10.100
 ip nhrp network-id 100
 ip nhrp holdtime 300
 ip nhrp nhs 192.168.30.1
 ip nhrp shortcut
 ip tcp adjust-mss 1360
 tunnel source Loopback0
 tunnel mode gre multipoint
ip route 172.10.10.0 255.255.255.0 Tunnel1030
ip route 172.10.70.0 255.255.255.0 Tunnel1030
```
Проверим, что DMVPN поднялся
```
R27#show ip nhrp 
192.168.30.1/32 via 192.168.30.1
   Tunnel1030 created 6d05h, never expire 
   Type: static, Flags: used 
   NBMA address: 10.10.10.100 
R27#show dm
R27#show dmvpn 
Legend: Attrb --> S - Static, D - Dynamic, I - Incomplete
        N - NATed, L - Local, X - No Socket
        # Ent --> Number of NHRP entries with same NBMA peer
        NHS Status: E --> Expecting Replies, R --> Responding, W --> Waiting
        UpDn Time --> Up or Down Time for a Tunnel
==========================================================================

Interface: Tunnel1030, IPv4 NHRP Details 
Type:Spoke, NHRP Peers:1, 

 # Ent  Peer NBMA Addr Peer Tunnel Add State  UpDn Tm Attrb
 ----- --------------- --------------- ----- -------- -----
     1 10.10.10.100       192.168.30.1    UP    6d05h     S
```

Настроим Чокурдах R28
```
interface Tunnel1030
 ip address 192.168.30.30 255.255.255.0
 no ip redirects
 ip mtu 1400
 ip nhrp map multicast 10.10.10.100
 ip nhrp map 192.168.30.1 10.10.10.100
 ip nhrp network-id 100
 ip nhrp holdtime 300
 ip nhrp nhs 192.168.30.1
 ip nhrp registration no-unique
 ip nhrp shortcut
 ip tcp adjust-mss 1360
 tunnel source Loopback30
 tunnel mode gre multipoint

ip route 172.10.10.0 255.255.255.0 192.168.30.1
ip route 172.10.70.0 255.255.255.0 192.168.30.1
```
Проверим, что DMVPN поднялся
```
R28# show ip nhrp 
192.168.30.1/32 via 192.168.30.1
   Tunnel1030 created 00:25:36, never expire 
   Type: static, Flags: used 
   NBMA address: 10.10.10.100 
R28# show DMVPN   
Legend: Attrb --> S - Static, D - Dynamic, I - Incomplete
        N - NATed, L - Local, X - No Socket
        # Ent --> Number of NHRP entries with same NBMA peer
        NHS Status: E --> Expecting Replies, R --> Responding, W --> Waiting
        UpDn Time --> Up or Down Time for a Tunnel
==========================================================================

Interface: Tunnel1030, IPv4 NHRP Details 
Type:Spoke, NHRP Peers:1, 

 # Ent  Peer NBMA Addr Peer Tunnel Add State  UpDn Tm Attrb
 ----- --------------- --------------- ----- -------- -----
     1 10.10.10.100       192.168.30.1    UP 00:25:41     S

```

Проверим доступность из Москвы
```
VPC#ping 172.10.35.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 172.10.35.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 3/4/5 ms
VPC#ping 172.10.31.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 172.10.31.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 4/4/5 ms
VPC#ping 172.10.30.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 172.10.30.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 3/4/6 ms
VPC#traceroute 172.10.35.1
Type escape sequence to abort.
Tracing the route to 172.10.35.1
VRF info: (vrf in name/id, vrf out name/id)
  1 172.10.10.252 2 msec 2 msec 0 msec
  2 10.10.0.110 2 msec 1 msec 1 msec
  3 10.10.0.113 2 msec 2 msec 2 msec
  4 192.168.30.35 6 msec 6 msec *
VPC#traceroute 172.10.30.1
Type escape sequence to abort.
Tracing the route to 172.10.30.1
VRF info: (vrf in name/id, vrf out name/id)
  1 172.10.10.252 1 msec 1 msec 1 msec
  2 10.10.0.100 2 msec 1 msec 2 msec
  3 10.10.0.107 2 msec 2 msec 2 msec
  4 192.168.30.30 5 msec 5 msec 4 msec
  5 10.30.0.101 4 msec 5 msec 4 msec
  6 172.10.30.1 7 msec 7 msec 4 msec
```
