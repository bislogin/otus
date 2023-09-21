# Лабороторная работа на тему:  
## Implement DHCPv4 

#### Цели  
Часть 1: Построение сети и настройка основных параметров устройства  
Часть 2: Настройка и проверка двух серверов DHCPv4 на R1  
Часть 3: Настройка и проверка DHCP-ретранслятора на R2  


#### Часть 1:  Построение сети и настройка основных параметров устройства
В части 1 мы настроим топологию сети и основные параметры на узлах ПК и коммутаторах.

##### Шаг 1: Создадим схему адресации  

Назначим сеть 192.168.1.0/24, чтобы она отвечала следующим требованиям:  
a. Одна подсеть, “Подсеть A”, поддерживающая 58 хостов (клиентская VLAN на R1).  
Подсеть A:
`192.168.1.0/26`

b. Одна подсеть, “Подсеть B”, поддерживающая 28 хостов (управляющая VLAN на R1).   
Подсеть B:
`192.168.1.64/27`

c. Одна подсеть, “Подсеть C”, поддерживающая 12 хостов (клиентская сеть на R2).  
Подсеть C: 
`192.168.1.96/28`  

##### Шаг 2: Подключим сеть кабелем, как показано в топологии.  

![Alt text](https://github.com/bislogin/otus/blob/main/labs/lab02/topology1.png)    

##### Шаг 3: Настроим  основные параметры для каждого маршрутизатора.  

```
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#hostname R1
R1(config)#no ip domain name 
R1(config)#enable secret class
R1(config)#line con 0
R1(config-line)#password cisco
R1(config-line)#login 
R1(config-line)#logging synchronous 
R1(config-line)#line vty 0 4
R1(config-line)#password cisco
R1(config-line)#login
R1(config-line)#logging synchronous 
R1(config-line)#exit
R1(config)#
R1(config)#service password-encryption 
R1(config)#Banner motd "This is a secure system. Authorized Access Only!"
R1(config)#do wr
Building configuration...
[OK]
R1(config)#
R1(config)#clock timezone MSK +3
R1(config)#
```

##### Шаг 4: Настроим маршрутизацию между VLAN на R1   

```
R1(config)#int gi0/1 
R1(config-if)#no shutdown 
R1(config-if)#int gi0/1.100
R1(config-subif)#description Clients
R1(config-subif)#encapsulation dot1Q 100
R1(config-subif)#ip address 192.168.1.1 255.255.255.192
R1(config-subif)#int gi0/1.200                         
R1(config-subif)#description Management                
R1(config-subif)#encapsulation dot1Q 200                
R1(config-subif)#ip address 192.168.1.65 255.255.255.224
R1(config-subif)#int gi0/1.1000                         
R1(config-subif)#description Native
R1(config-subif)#encapsulation dot1Q 1000 native 
R1(config-subif)#do show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0         unassigned      YES unset  administratively down down    
GigabitEthernet0/1         unassigned      YES unset  up                    up      
GigabitEthernet0/1.100     192.168.1.1     YES manual up                    up      
GigabitEthernet0/1.200     192.168.1.65    YES manual up                    up      
GigabitEthernet0/1.1000    unassigned      YES unset  up                    up      
GigabitEthernet0/2         unassigned      YES unset  administratively down down    
GigabitEthernet0/3         unassigned      YES unset  administratively down down
```

##### Шаг 5: Настроим G0/1 на R2, затем G0/0 и статическую маршрутизацию для обоих маршрутизаторов  

```
R2(config-if)#int gi0/0
R2(config-if)#no shutdown 
*Sep 20 18:12:04.254: %LINK-3-UPDOWN: Interface GigabitEthernet0/0, changed state to up
*Sep 20 18:12:05.254: %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up
R2(config-if)#ip address 10.0.0.2 255.255.255.252
R2(config-if)#exit
R2(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.1
R2(config)#do wr
```

```
R1(config)#int gi0/0
R1(config-if)#no shutdown 
*Sep 20 18:12:46.870: %LINK-3-UPDOWN: Interface GigabitEthernet0/0, changed state to up
*Sep 20 18:12:47.870: %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up
R1(config-if)#ip address 10.0.0.1 255.255.255.252
R1(config-if)#exit
R1(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.2
R1(config)#do ping 192.168.1.97
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.97, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms
R1(config)#do wr
```

##### Шаг 6: Настроим основные параметры для каждого коммутатора.     

```
Switch>en
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname S1
S1(config)#no ip domain name 
S1(config)#enable secret class
S1(config)#line con 0
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#logging synchronous 
S1(config-line)#line vty 0 4
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#logging synchronous
S1(config-line)#exit
S1(config)#service password-encryption 
S1(config)#Banner motd "This is a secure system. Authorized Access Only!"
S1(config)#clock timezone MSK +3
S1(config)#do wr
```

##### Шаг 7: Создадим VLAN на S1. 

```
S1(config-if)#exit
S1(config)#vlan 100
S1(config-vlan)#name Clients
S1(config)#vlan 200
S1(config-vlan)#name Management
S1(config-vlan)#int vlan 200
S1(config-if)#ip address 192.168.1.66 255.255.255.224
S1(config-if)#no shutdown 
S1(config-if)#exit      
S1(config)#ip route 0.0.0.0 0.0.0.0 192.168.1.65
S1(config)#vlan 999
S1(config-vlan)#name Parking_Lot
S1(config-vlan)#do show int status

Port      Name               Status       Vlan       Duplex  Speed Type 
Gi0/0                        connected    1          a-full   auto RJ45
Gi0/1                        connected    1          a-full   auto RJ45
Gi0/2                        connected    1          a-full   auto RJ45
Gi0/3                        connected    1          a-full   auto RJ45
S1(config-vlan)#int ran gi0/2-3
S1(config-if-range)#switchport mode access 
S1(config-if-range)#switchport access vlan 999
S1(config-if-range)#shutdown 
S1(config-if-range)#do show int status        

Port      Name               Status       Vlan       Duplex  Speed Type 
Gi0/0                        connected    1          a-full   auto RJ45
Gi0/1                        connected    1          a-full   auto RJ45
Gi0/2                        disabled     999          auto   auto RJ45
Gi0/3                        disabled     999          auto   auto RJ45
```

```
S2(config)#int vlan 1
S2(config-if)#ip address 192.168.1.98 255.255.255.240
S2(config-if)#no shutdown
S2(config-if)#exit
S2(config)#ip route 0.0.0.0 0.0.0.0 192.168.1.97
S2(config)#interface range gi0/2-3
S2(config-if-range)#shutdown 
S2(config-if-range)#do show int status

Port      Name               Status       Vlan       Duplex  Speed Type 
Gi0/0                        connected    1          a-full   auto RJ45
Gi0/1                        connected    1          a-full   auto RJ45
Gi0/2                        disabled     1            auto   auto RJ45
Gi0/3                        disabled     1            auto   auto RJ45
```

##### Шаг 8: Назначим VLAN правильным интерфейсам коммутатора.

```
S1(config)#int gi0/1 
S1(config-if)#switchport trunk encapsulation dot1q 
S1(config-if)#switchport mode trunk
S1(config-if)#switchport trunk native vlan 1000
S1(config-if)#switchport trunk allowed vlan 100,200,1000
S1(config-if)#int gi0/0
S1(config-if)#switchport mode access 
S1(config-if)#switchport access vlan 100
S1(config-if)#do show int trunk

Port        Mode             Encapsulation  Status        Native vlan
Gi0/1       on               802.1q         trunking      1000

Port        Vlans allowed on trunk
Gi0/1       100,200,1000

Port        Vlans allowed and active in management domain
Gi0/1       200

Port        Vlans in spanning tree forwarding state and not pruned
Gi0/1       200
```

Вопрос:
Почему интерфейс на S2 Gi0/1 указан в разделе VLAN 1?
> Линк на R2, который смотрит в сторону S2, не является транковым и находится в VLAN1.   

Вопрос:
На данный момент, какой IP-адрес был бы у ПК, если бы они были подключены к сети с помощью DHCP?
> 192.168.1.67

#### Часть 2: Настройка и проверка двух серверов DHCPv4 на R1   

В части 2 мы настроим и проверим сервер DHCPv4 на R1. Сервер DHCPv4 будет обслуживать две подсети, подсеть A и подсеть C.   

##### Шаг 1: Настроим R1 с помощью пулов DHCPv4 для двух поддерживаемых подсетей. Ниже приведен только пул DHCP для подсети А

```
R1(config)#ip dhcp excluded-address 192.168.1.2 192.168.1.6           
R1(config)#ip dhcp excluded-address 192.168.1.98 192.168.1.102
R1(config)#ip dhcp pool Client_LAN
R1(dhcp-config)#network 192.168.1.0 /26 
R1(dhcp-config)#domain-name ccna-lab.com
R1(dhcp-config)#default-router 192.168.1.1
R1(dhcp-config)#lease 2 12 30 
R1(dhcp-config)#ip dhcp pool R2_Client_LAN
R1(dhcp-config)#network 192.168.1.96 /28
R1(dhcp-config)#default-router 192.168.1.97
R1(dhcp-config)#domain-name ccna-lab.com
R1(dhcp-config)#lease 2 12 30
```
##### Шаг 2: Сохраниим вашу конфигурацию   

```
R1(dhcp-config)#do wr
Building configuration...
[OK]
R1(dhcp-config)#
```

##### Шаг 3: Проверим конфигурацию сервера DHCPv4
a. Выполним команду show ip dhcp pool, чтобы просмотреть сведения о пуле.   
```
R1#show ip dhcp pool

Pool Client_LAN :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0 
 Total addresses                : 62
 Leased addresses               : 0
 Pending event                  : none
 1 subnet is currently in the pool :
 Current index        IP address range                    Leased addresses
 192.168.1.1          192.168.1.1      - 192.168.1.62      0

Pool R2_Client_LAN :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0 
 Total addresses                : 14
 Leased addresses               : 0
 Pending event                  : none
 1 subnet is currently in the pool :
 Current index        IP address range                    Leased addresses
 192.168.1.97         192.168.1.97     - 192.168.1.110     0
R1#
```

b. Выполним команду show ip dhcp binding, чтобы проверить установленные назначения DHCP-адресов.   
```
R1#show ip dhcp binding 
Bindings from all pools not associated with VRF:
IP address          Client-ID/              Lease expiration        Type
                    Hardware address/
                    User name
R1#
```
c. Выполним команду show ip dhcp server statistics для проверки DHCP-сообщений.   
```
R1#show ip dhcp server statistics
Memory usage         25308
Address pools        2
Database agents      0
Automatic bindings   0
Manual bindings      0
Expired bindings     0
Malformed messages   0
Secure arp entries   0

Message              Received
BOOTREQUEST          0
DHCPDISCOVER         0
DHCPREQUEST          0
DHCPDECLINE          0
DHCPRELEASE          0
DHCPINFORM           0

Message              Sent
BOOTREPLY            0
DHCPOFFER            0
DHCPACK              0
DHCPNAK              0
R1#
```

##### Шаг 4: Попытаемся получить IP-адрес из DHCP на ПК-A      
```
VPCS> ip dhcp
DDORA IP 192.168.1.7/26 GW 192.168.1.1

VPCS> ping 192.168.1.65

84 bytes from 192.168.1.65 icmp_seq=1 ttl=255 time=1.486 ms
84 bytes from 192.168.1.65 icmp_seq=2 ttl=255 time=1.300 ms
84 bytes from 192.168.1.65 icmp_seq=3 ttl=255 time=1.252 ms
84 bytes from 192.168.1.65 icmp_seq=4 ttl=255 time=1.690 ms
84 bytes from 192.168.1.65 icmp_seq=5 ttl=255 time=1.187 ms

VPCS> show ip

NAME        : VPCS[1]
IP/MASK     : 192.168.1.7/26
GATEWAY     : 192.168.1.1
DNS         : 
DHCP SERVER : 192.168.1.1
DHCP LEASE  : 217795, 217800/108900/190575
DOMAIN NAME : ccna-lab.com
MAC         : 00:50:79:66:68:10
LPORT       : 20000
RHOST:PORT  : 127.0.0.1:30000
MTU         : 1500

VPCS> 
```

#### Часть 3: Настройка и проверка DHCP-ретранслятора на R2    

##### Шаг 1: Настроим R2 в качестве агента ретрансляции DHCP для локальной сети на G0/1   

```
R2(config)#int gi0/1
R2(config-if)#ip helper-address 10.0.0.1
R2(config-if)#do wr
Building configuration...
[OK]
R2(config-if)#
```

##### Шаг 2: Попытайемся получить IP-адрес из DHCP на ПК-B   
```
VPCS> ip dhcp
DDORA IP 192.168.1.103/28 GW 192.168.1.97

VPCS> ping 192.168.1.1

84 bytes from 192.168.1.1 icmp_seq=1 ttl=254 time=2.714 ms
84 bytes from 192.168.1.1 icmp_seq=2 ttl=254 time=1.830 ms
84 bytes from 192.168.1.1 icmp_seq=3 ttl=254 time=1.698 ms
84 bytes from 192.168.1.1 icmp_seq=4 ttl=254 time=1.822 ms
84 bytes from 192.168.1.1 icmp_seq=5 ttl=254 time=1.673 ms

VPCS> show ip

NAME        : VPCS[1]
IP/MASK     : 192.168.1.103/28
GATEWAY     : 192.168.1.97
DNS         : 
DHCP SERVER : 10.0.0.1
DHCP LEASE  : 217752, 217800/108900/190575
DOMAIN NAME : ccna-lab.com
MAC         : 00:50:79:66:68:11
LPORT       : 20000
RHOST:PORT  : 127.0.0.1:30000
MTU         : 1500

VPCS>
```

Выполним команду ip dhcp binding на R1 для проверки привязок DHCP.   
```
R1#show ip dhcp binding 
Bindings from all pools not associated with VRF:
IP address          Client-ID/              Lease expiration        Type
                    Hardware address/
                    User name
192.168.1.7         0100.5079.6668.10       Sep 23 2023 11:10 AM    Automatic
192.168.1.103       0100.5079.6668.11       Sep 23 2023 11:18 AM    Automatic
R1#
```
Выполним команду show ip dhcp server statistics на R1 и R2 для проверки сообщений DHCP.   
```
R1#show ip dhcp server statistics 
Memory usage         42322
Address pools        2
Database agents      0
Automatic bindings   2
Manual bindings      0
Expired bindings     0
Malformed messages   0
Secure arp entries   0

Message              Received
BOOTREQUEST          0
DHCPDISCOVER         10
DHCPREQUEST          2
DHCPDECLINE          0
DHCPRELEASE          0
DHCPINFORM           0

Message              Sent
BOOTREPLY            0
DHCPOFFER            2
DHCPACK              2
DHCPNAK              0
R1#
```

```
R2#show ip dhcp server statistics 
Memory usage         22565
Address pools        0
Database agents      0
Automatic bindings   0
Manual bindings      0
Expired bindings     0
Malformed messages   0
Secure arp entries   0

Message              Received
BOOTREQUEST          0
DHCPDISCOVER         0
DHCPREQUEST          0
DHCPDECLINE          0
DHCPRELEASE          0
DHCPINFORM           0

Message              Sent
BOOTREPLY            0
DHCPOFFER            0
DHCPACK              0
DHCPNAK              0
R2#
```
