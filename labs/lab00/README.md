# Лабороторная работа на тему:
## Configure Router-on-a-Stick Inter-VLAN Routing
#### Топология

![Alt text](https://github.com/bislogin/otus/blob/main/labs/lab00/topology.jpg)

#### Цели

Часть 1: Построение сети и настройка основных параметров устройства  
Часть 2: Создание виртуальных сетей и назначение портов коммутатора  
Часть 3: Настройка магистрали 802.1Q между коммутаторами  
Часть 4: Настройка маршрутизации между VLAN на маршрутизаторе  
Часть 5: Убедитесь, что маршрутизация между VLAN работает  

#### Таблица Адресации

<table>
    <thead>
        <tr>
            <th>VLAN</th>
            <th>Name</th>
            <th>Interface Assigned</th>
			<th>Subnet Mask</th>
            <th>Default Gateway</th>
        </tr>
    </thead>
    <tbody>
        <tr>
			<td align="center">R1</td>
			<td align="center">G0/0/1.3</td>
			<td align="center">192.168.3.1</td>
			<td align="center">255.255.255.0</td>
            <td rowspan=3 align="center">N/A</td>
        </tr>
        <tr>
            <td align="center"> </td>
			<td align="center">G0/0/1.4</td>
			<td align="center">192.168.4.1</td>
			<td align="center">255.255.255.0</td>
        </tr>
		<tr>
            <td align="center"> </td>
			<td align="center">G0/0/1.8	</td>
			<td align="center">N/A</td>
			<td align="center">N/A</td>
        </tr>
        <tr>
			<td align="center">S1</td>
			<td align="center">VLAN 3</td>
			<td align="center">192.168.3.11</td>
			<td align="center">255.255.255.0</td>
            <td align="center">192.168.3.1</td>
        </tr>
        <tr>
			<td align="center">S2</td>
			<td align="center">VLAN 3</td>
			<td align="center">192.168.3.12</td>
			<td align="center">255.255.255.0</td>
            <td align="center">192.168.3.1</td>
        </tr>
        <tr>
			<td align="center">PC-A</td>
			<td align="center">NIC</td>
			<td align="center">192.168.3.3</td>
			<td align="center">255.255.255.0</td>
            <td align="center">192.168.3.1</td>
        </tr>
        <tr>
			<td align="center">PC-B</td>
			<td align="center">NIC</td>
			<td align="center">192.168.4.3</td>
			<td align="center">255.255.255.0</td>
            <td align="center">192.168.4.1</td>
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
			<td align="left">S1: Gi0/3, G1/0-3 </td>
        </tr>
        <tr>
			<td align="left">S2: G0/2-3, G1/0-3 </td>
        </tr>
        <tr>
			<td align="left">8</td>
			<td align="left">Native</td>
			<td align="left">N/A</td>
        </tr>
    </tbody>
</table>
