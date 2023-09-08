### Часть 1: Построение сети и настройка основных параметров устройства

В части 1 мы настроим топологию сети и основные параметры на узлах ПК и коммутаторах.

#### Шаг 1: Подключение сети, как показано в топологии.
![Alt text](https://github.com/bislogin/otus/blob/main/labs/lab00/ipv4/%D0%91%D0%B5%D0%B7%D1%8B%D0%BC%D1%8F%D0%BD%D0%BD%D1%8B%D0%B9.png)

#### Таблица Адресации

<table>
    <thead>
        <tr>
            <th>VLAN</th>
            <th>Name</th>
            <th>Interface Assigned</th>
			<th>Prefix</th>
            <th>Default Gateway</th>
        </tr>
    </thead>
    <tbody>
        <tr>
			<td align="center">R1</td>
			<td align="center">G0/0.3</td>
			<td align="center">fc0::3:1</td>
			<td align="center">118</td>
            <td rowspan=3 align="center">N/A</td>
        </tr>
        <tr>
            <td align="center"> </td>
			<td align="center">G0/0.4</td>
			<td align="center">fc0::4:1</td>
			<td align="center">118</td>
        </tr>
		<tr>
            <td align="center"> </td>
			<td align="center">G0/0.8	</td>
			<td align="center">N/A</td>
			<td align="center">N/A</td>
        </tr>
        <tr>
			<td align="center">S1</td>
			<td align="center">VLAN 3</td>
			<td align="center">fc0::3:11</td>
			<td align="center">118</td>
            <td align="center">fc0::3:1</td>
        </tr>
        <tr>
			<td align="center">S2</td>
			<td align="center">VLAN 3</td>
			<td align="center">fc0::3:12</td>
			<td align="center">118</td>
            <td align="center">fc0::3:1</td>
        </tr>
        <tr>
			<td align="center">PC-A</td>
			<td align="center">NIC</td>
			<td align="center">fc0::3:3</td>
			<td align="center">118</td>
            <td align="center">fc0::3:1</td>
        </tr>
        <tr>
			<td align="center">PC-B</td>
			<td align="center">NIC</td>
			<td align="center">fc0::4:3</td>
			<td align="center">118</td>
            <td align="center">fc0::4:1</td>
        </tr>
    </tbody>
</table>

#### Таблица VLAN

<table>
    <thead>
        <tr>
            <th>VLAN</th>
            <th>Name</th>
            <th>Interface Assigned</th>
        </tr>
    </thead>
    <tbody>
        <tr>
			<td rowspan=3 align="left">3</td>
			<td rowspan=3 align="left">Management</td>
			<td align="left">S1: VLAN 3</td>
        </tr>
        <tr>
			<td align="left">S2: VLAN 3</td>
        </tr>
        <tr>
			<td align="left">S1: Gi0/2</td>
        </tr>
		<tr>
            <td align="left">4 </td>
			<td align="left">Operations	</td>
			<td align="left">S2: Gi0/0</td>
        </tr>
        <tr>
			<td rowspan=2 align="left">7</td>
			<td rowspan=2 align="left">ParkingLot</td>
			<td align="left">S1: Gi0/3, Gi1/0-3 </td>
        </tr>
        <tr>
			<td align="left">S2: G0/2-3, Gi1/0-3 </td>
        </tr>
        <tr>
			<td align="left">8</td>
			<td align="left">Native</td>
			<td align="left">N/A</td>
        </tr>
    </tbody>
</table>


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

> VPCS> ip fc0::3:3/118 fc0::3:1  
> PC1 : fc0::3:3/118


> VPCS> ip fc0::4:3/118 fc0::4:1  
> PC1 : fc0::4:3/118  


### Часть 2: Создание виртуальных сетей и назначение портов коммутатора

В части 2 мы создадим виртуальные сети, как указано в таблице на обоих коммутаторах. Затем назначим виртуальные сети соответствующему интерфейсу.

#### Шаг 1: Создаим виртуальные сети на обоих коммутаторах.

a.	Create and name the required VLANs on each switch from the table above.
> S1(config)#vlan 3  
> S1(config-vlan)#name Management  
> S1(config-vlan)#vlan 4           
> S1(config-vlan)#name Operations  
> S1(config-vlan)#vlan 7           
> S1(config-vlan)#name ParkingLot  
> S1(config-vlan)#vlan 8           
> S1(config-vlan)#name Native     

b.	Configure the management interface and default gateway on each switch using the IP address information in the Addressing Table. 
> S1(config)#int vlan 3  
> S1(config-if)#ipv6 address fc0::3:11/118  

> S1(config)#int vlan 3  
> S1(config-if)#ipv6 address fc0::3:12/118     

c.	Assign all unused ports on both switches to the ParkingLot VLAN, configure them for static access mode, and administratively deactivate them.
> S1(config)#int range gi0/3, gi1/0-3   
> S1(config-if-range)#switchport mode access   
> S1(config-if-range)#switchport access vlan 7  

> S2(config)#int range gi0/2-3, gi1/0-3     
> S2(config-if-range)#switchport mode access   
> S2(config-if-range)#switchport access vlan 7  

#### Шаг 2: Назнаим VLAN правильным интерфейсам коммутатора.

a.	Assign used ports to the appropriate VLAN (specified in the VLAN table above) and configure them for static access mode. Be sure to do this on both switches
> S1(config)#int gi0/2  
> S1(config-if)#switchport mode access   
> S1(config-if)#switchport access vlan 3

> S2(config-if)#int gi0/0                 
> S2(config-if)#switchport mode access   
> S2(config-if)#switchport access vlan 4  

b.	Issue the show vlan brief command and verify that the VLANs are assigned to the correct interfaces.
![Alt text](https://github.com/bislogin/otus/blob/main/labs/lab00/ipv4/image.png)

![Alt text](https://github.com/bislogin/otus/blob/main/labs/lab00/ipv4/vlan%20br%20s2.png)

### Часть 3: Настройка магистрали 802.1Q между коммутаторами

#### Шаг 1: Вручную настроим магистральный интерфейс Gi0/1.

a.	Change the switchport mode on interface Gi0/1 to force trunking. Make sure to do this on both switches.
> S1(config)#int gi0/1  
> S1(config-if)#switchport trunk encapsulation dot1q   
> S1(config-if)#switchport mode trunk   

b.	As a part of the trunk configuration, set the native VLAN to 8 on both switches. You may see error messages temporarily while the two interfaces are configured for different native VLANs.
> S1(config-if)#switchport trunk native vlan 8  

c.	As another part of trunk configuration, specify that VLANs 3, 4, and 8 are only allowed to cross the trunk.
> S1(config-if)#switchport trunk allowed vlan 3,4,8

d.	Issue the show interfaces trunk command to verify trunking ports, the Native VLAN and allowed VLANs across the trunk.
![Alt text](https://github.com/bislogin/otus/blob/main/labs/lab00/ipv4/tr%20s1.png)

![Alt text](https://github.com/bislogin/otus/blob/main/labs/lab00/ipv4/tr%20s2.png)

#### Шаг 2: Вручную настройте магистральный интерфейс S1 Gi0/0

a.	Configure the Gi0/0 on S1 with the same trunk parameters as Gi0/1. This is the trunk to the router.
> S1(config)#int gi0/0  
> S1(config-if)#switchport trunk encapsulation dot1q   
> S1(config-if)#switchport mode trunk   
> S1(config-if)#switchport trunk allowed vlan 3,4,8  
> S1(config-if)#switchport trunk native vlan 8  

b.	Save the running configuration to the startup configuration file on S1 and S2.
> S1#wr  

c.	Issue the show interfaces trunk command to verify trunking.

![Alt text](https://github.com/bislogin/otus/blob/main/labs/lab00/ipv4/sh%20ip%20int%20br%202.png)


### Часть 4: Настроим маршрутизацию между VLAN на маршрутизаторе

a.	Activate interface G0/0 on the router.
> R1(config)#ipv6 unicast-routing   
> R1(config)#int gi0/0  
> R1(config-if)#no shutdown   

b.	Configure sub-interfaces for each VLAN as specified in the IP addressing table. All sub-interfaces use 802.1Q encapsulation. Ensure the sub-interface for the native VLAN does not have an IP address assigned. Include a description for each sub-interface.

> R1(config)#int gi0/0.3  
> R1(config-subif)#description Management  
> R1(config-subif)#encapsulation dot1Q 3  
> R1(config-subif)#ipv6 address fc0::3:1/118    
> R1(config-subif)#int gi0/0.4             
> R1(config-subif)#description Operations  
> R1(config-subif)#encapsulation dot1Q 4   
> R1(config-subif)#ipv6 address fc0::4:1/118    
> R1(config-subif)#int gi0/0.8             
> R1(config-subif)#description Native      
> R1(config-subif)#encapsulation dot1Q 8  

c.	Use the show ipv6 interface brief command to verify the sub-interfaces are operational.

![Alt text](https://github.com/bislogin/otus/blob/main/labs/lab00/ipv6/v6%20ip%20br.png)

### Часть 5: Убедимся, что маршрутизация между VLAN работает

#### Шаг 1: Выполним следующие тесты с помощью PC-A. Все должно быть успешно.

a.	Ping from PC-A to its default gateway.

![Alt text](https://github.com/bislogin/otus/blob/main/labs/lab00/ipv6/v6%20ping%20gw.png)

b.	Ping from PC-A to PC-B

![Alt text](https://github.com/bislogin/otus/blob/main/labs/lab00/ipv6/v6%20ping%20pc1%20to%20pc2.png)

c.	Ping from PC-A to S2

![Alt text](https://github.com/bislogin/otus/blob/main/labs/lab00/ipv6/v6%20ping%20pc1%20to%20s2.png)

#### Шаг 2: Выполним следующий тест с помощью PC-B

From the command prompt on PC-B, issue the tracert command to the address of PC-A.

![Alt text](https://github.com/bislogin/otus/blob/main/labs/lab00/ipv6/v6%20trace%20pc2%20to%20pc1.png)

В трассировке отображается промежуточный адрес fc0::4:1  
Это саб интерфейс на S1 Gi0/0.4 
