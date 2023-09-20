# Лабороторная работа на тему:  
## Implement DHCPv6

#### Цели     
Часть 1: Построение сети и настройка основных параметров устройства   
Часть 2: Проверьте назначение адреса SLAAC с помощью R1   
Часть 3: Настройка и проверка сервера DHCPv6 без сохранения состояния на R1   
Часть 4: Настройка и проверка DHCPv6-сервера с отслеживанием состояния на R1   
Часть 5: Настройка и проверка реле DHCPv6 на R2   

#### Часть 1: Построение сети и настройка основных параметров устройства

##### Шаг 1: Подключим сеть кабелем, как показано в топологии.

![Alt text](https://github.com/bislogin/otus/blob/main/labs/lab02/topology1.png)       

##### Шаг 2: Настроим основные параметры для каждого коммутатора.

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

##### Шаг 3: Настроим основные параметры для каждого маршрутизатора.

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
R1(config)#clock timezone MSK +3
R1(config)#ipv6 unicast-routing
R1(config)#do wr
Building configuration...
[OK]
R1(config)#
```

##### Шаг 4: Настроим интерфейсы и маршрутизацию для обоих маршрутизаторов.

```
R1(config)#int gi0/0
R1(config-if)#ipv6 enable
R1(config-if)#ipv6 address 2001:db8:acad:2::1/64         
R1(config-if)#ipv6 address fe80::1 link-local 
R1(config-if)#int gi0/1
R1(config-if)#ipv6 enable 
R1(config-if)#ipv6 address 2001:db8:acad:1::1/64
R1(config-if)#ipv6 address fe80::1 link-local    
R1(config-if)#ipv6 route ::/0 2001:db8:acad:2::2
```

```
R2(config)#int gi0/0
R2(config-if)#ipv6 enable 
R2(config-if)#ipv6 address 2001:db8:acad:2::2/64        
R2(config-if)#ipv6 address fe80::2 link-local 
R2(config-if)#int gi0/1                         
R2(config-if)#ipv6 enable                       
R2(config-if)#ipv6 address 2001:db8:acad:3::1/64
R2(config-if)#ipv6 address fe80::1 link-local   
R2(config-if)#ipv6 route ::/0 2001:db8:acad:2::1
R2(config)#do ping 2001:db8:acad:1::1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:DB8:ACAD:1::1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/3 ms
R2(config)#
```

#### Часть 2: Проверим назначение адреса SLAAC с помощью R1

```
VPCS> ip auto
GLOBAL SCOPE      : 2001:db8:acad:1:2050:79ff:fe66:6810/64
ROUTER LINK-LAYER : 50:00:00:14:00:01
```


#### Часть 3: Настройка и проверка сервера DHCPv6 на R1   
В части 3 мы настроите и проверите DHCP-сервер без сохранения состояния на R1. Цель состоит в том, чтобы предоставить PC-A информацию о DNS-сервере и домене.   

##### Шаг 1: Изучиим конфигурацию PC-A более подробно.   
```
VPCS> show ipv6

NAME              : VPCS[1]
LINK-LOCAL SCOPE  : fe80::250:79ff:fe66:6810/64
GLOBAL SCOPE      : 2001:db8:acad:1:2050:79ff:fe66:6810/64
DNS               : 
ROUTER LINK-LAYER : 50:00:00:14:00:01
MAC               : 00:50:79:66:68:10
LPORT             : 20000
RHOST:PORT        : 127.0.0.1:30000
MTU:              : 1500
```

Обратим внимание, что первичный DNS-суффикс отсутствует.   

