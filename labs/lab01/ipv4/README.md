### Часть 1: Создание сети и настройка основных параметров устройства

В части 1 мы настроим топологию сети и основные параметры маршрутизаторов.

#### Шаг 1: Подключение сети, как показано в топологии.

![Alt text](https://github.com/bislogin/otus/blob/main/labs/lab01/ipv4/photo_2023-09-16_10-55-54.jpg)

##### Шаг 2:	Выполните инициализацию и перезагрузку коммутаторов.

##### Шаг 3:	Настройте базовые параметры каждого коммутатора.

a.	Отключим поиск DNS.
> Switch>en  
> Switch#conf t  
> Enter configuration commands, one per line.  End with CNTL/Z.  
> Switch(config)#no ip domain-lookup  

b.	Присвоим имена устройствам в соответствии с топологией.
> Switch(config)#hostname S1  

c.	Назначим class в качестве зашифрованного пароля доступа к привилегированному режиму.
> S1(config)#enable secret class  

d.	Назначем cisco в качестве паролей консоли и VTY и активируйте вход для консоли и VTY каналов.
> S1(config)#line con 0  
> S1(config-line)#password cisco  
> S1(config-line)#login  
> S1(config-line)#logging synchronous   

> S1(config-line)#line vty 0 4  
> S1(config-line)#password cisco    
> S1(config-line)#login  
> S1(config-line)#logging synchronous   

e.	Настроим баннерное сообщение дня (MOTD) для предупреждения пользователей о запрете несанкционированного доступа.
> S1(config)#Banner motd "This is a secure system. Authorized Access Only!"  

f.	Зададим IP-адрес, указанный в таблице адресации для VLAN 1 на всех коммутаторах.
> S1(config)#int vlan 1  
> S1(config-if)#ip address 192.168.1.1 255.255.255.0  

g.	Скопируйте текущую конфигурацию в файл загрузочной конфигурации.
> S1#wr  
> Building configuration...  
> Compressed configuration from 3244 bytes to 1619 bytes[OK]  
> S1#  

