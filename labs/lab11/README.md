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



##### 3. Настроите статический NAT для R20.  



##### 4. Настроите NAT так, чтобы R19 был доступен с любого узла для удаленного управления.  
####     5*. Настроите статический NAT(PAT) для офиса Чокурдах.  



##### 5. Настроите для IPv4 DHCP сервер в офисе Москва на маршрутизаторах R12 и R13. VPC1 и VPC7 должны получать сетевые настройки по DHCP.  



##### 6. Настроите NTP сервер на R12 и R13. Все устройства в офисе Москва должны синхронизировать время с R12 и R13.  
