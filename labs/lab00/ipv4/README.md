### Часть 1: Построение сети и настройка основных параметров устройства

В части 1 мы настроим топологию сети и основные параметры на узлах ПК и коммутаторах.

#### Шаг 1: Подключение сети, как показано в топологии.
![Alt text](https://github.com/bislogin/otus/blob/main/labs/lab00/ipv4/%D0%91%D0%B5%D0%B7%D1%8B%D0%BC%D1%8F%D0%BD%D0%BD%D1%8B%D0%B9.png)

#### Шаг 2: Настройка основных параметров маршрутизатора.

a. Console into the router and enable privileged EXEC mode.
> Router>en  
> Router#

b. Enter configuration mode.
> Router#conf t

c. Assign a device name to the router.
> Router(config)#hostname R1

d.	Disable DNS lookup to prevent the router from attempting to translate incorrectly entered commands as though they were host names.  
> R1(config)#no ip domain lookup

e.	Assign class as the privileged EXEC encrypted password.  
> R1(config)#enable secret class

f.	Assign cisco as the console password and enable login.  
> R1(config)#line console 0  
> R1(config-line)#password cisco  
> R1(config-line)#login    
> R1(config-line)#logging synchronous

g.	Assign cisco as the VTY password and enable login.  
> R1(config)#line vty 0 4  
> R1(config-line)#password cisco  
> R1(config-line)#login  
> R1(config-line)#logging synchronous

h.	Encrypt the plaintext passwords.
> R1(config)#service password-encryption

i.	Create a banner that warns anyone accessing the device that unauthorized access is prohibited.
> R1(config)#Banner motd "This is a secure system. Authorized Access Only!"

j.	Save the running configuration to the startup configuration file.
> R1(config)# do wr

k.	Set the clock on the router.
> R1(config)#clock timezone MSK +3



#### Шаг 3: Настройка основных параметров для каждого коммутатора.

a.	Console into the switch and enable privileged EXEC mode.
> Switch>en  
> Switch#

b.	Enter configuration mode.
> Switch#conf t  

c.	Assign a device name to the switch.
> Switch(config)#hostname S1

d.	Disable DNS lookup to prevent the router from attempting to translate incorrectly entered commands as though they were host names.
> S1(config)#no ip domain lookup

e.	Assign class as the privileged EXEC encrypted password.
> S1(config)#enable secret class  

f.	Assign cisco as the console password and enable login.
> S1(config)#line console 0  
> S1(config-line)#password cisco  
> S1(config-line)#login    
> S1(config-line)#logging synchronous

g.	Assign cisco as the vty password and enable login.
> S1(config)#line vty 0 4  
> S1(config-line)#password cisco  
> S1(config-line)#login  
> S1(config-line)#logging synchronous

h.	Encrypt the plaintext passwords.
> S1(config)#service password-encryption

i.	Create a banner that warns anyone accessing the device that unauthorized access is prohibited.
> S1(config)#Banner motd "This is a secure system. Authorized Access Only!"

j.	Set the clock on the switch.
> S1(config)#clock timezone MSK +3   

k.	Copy the running configuration to the startup configuration.
> S1(config)#do wr  

#### Шаг 4: Настройка хостов ПК.

> VPCS> ip 192.168.3.3/24 192.168.3.1  
> Checking for duplicate address...  
> VPCS : 192.168.3.3 255.255.255.0 gateway 192.168.3.1


> VPCS> ip 192.168.4.3/24 192.168.4.1  
> Checking for duplicate address...  
> VPCS : 192.168.4.3 255.255.255.0 gateway 192.168.4.1


### Часть 2: Создание виртуальных сетей и назначение портов коммутатора

В части 2 мы создадим виртуальные сети, как указано в таблице на обоих коммутаторах. Затем назначим виртуальные сети соответствующему интерфейсу.

#### Шаг 1: Создать виртуальные сети на обоих коммутаторах.

a.	Create and name the required VLANs on each switch from the table above.


b.	Configure the management interface and default gateway on each switch using the IP address information in the Addressing Table. 


c.	Assign all unused ports on both switches to the ParkingLot VLAN, configure them for static access mode, and administratively deactivate them.



