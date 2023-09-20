# Лабороторная работа на тему:  
## Implement DHCP  
#### Топология  

![Alt text](https://github.com/bislogin/otus/blob/main/labs/lab02/topology.png)  

#### Addressing Table ipv4

<table>
    <thead>
        <tr>
            <th>Device</th>
            <th>Interface</th>
            <th>IP Address</th>
            <th>Subnet Mask</th>
            <th>Default Gateway</th>
        </tr>
    </thead>
    <tbody>
        <tr>
			      <td rowspan=5 align="left">R1</td>
			      <td align="left">Gi0/0</td>
			      <td align="left">10.0.0.1</td>
			      <td align="left">255.255.255.252</td>
            <td rowspan=5 align="left">N/A</td>
        </tr>
        <tr>
			      <td align="left">Gi0/1</td>
			      <td align="left">N/A</td>
			      <td align="left">N/A</td>
        </tr>
	    	<tr>
			      <td align="left">Gi0/1.100	</td>
			      <td align="left">192.168.1.1 </td>
			      <td align="left">255.255.255.192 </td>
        </tr>
      	<tr>
			      <td align="left">Gi0/1.200	</td>
			      <td align="left">192.168.1.65 </td>
			      <td align="left">255.255.255.224 </td>
        </tr>
	    	<tr>
			      <td align="left">Gi0/1.1000	</td>
			      <td align="left">N/A</td>
			      <td align="left">N/A</td>
        </tr>
        <tr>
			      <td rowspan=2 align="left">R2</td>
		      	<td align="left">Gi0/0</td>
		      	<td align="left">10.0.02</td>
			      <td align="left">255.255.255.252</td>
            <td rowspan=2 align="left">N/A</td>
        </tr>
      	<tr>
			      <td align="left">Gi0/1</td>
			      <td align="left">192.168.1.97 </td>
			      <td align="left">255.255.255.240 </td>
        </tr>
        <tr>
			      <td align="left">S1</td>
	      		<td align="left">VLAN 200</td>
	      		<td align="left">192.168.1.66 </td>
		      	<td align="left">255.255.255.224 </td>
            <td align="left">192.168.1.65 </td>
        </tr>
        <tr>
			      <td align="left">S2</td>
	      		<td align="left">VLAN 1</td>
	      		<td align="left"> </td>
		      	<td align="left"> </td>
            <td align="left"> </td>
        </tr>
        <tr>
		      	<td align="center">PC-A</td>
	      		<td align="center">NIC</td>
	      		<td align="center">DHCP</td>
	      		<td align="center">DHCP</td>
            <td align="center">DHCP</td>
        </tr>
        <tr>
      			<td align="center">PC-B</td>
      			<td align="center">NIC</td>
      			<td align="center">DHCP</td>
      			<td align="center">DHCP</td>
            <td align="center">DHCP</td>
        </tr>
    </tbody>
</table>

#### Addressing Table ipv6

<table>
    <thead>
        <tr>
            <th>Device</th>
            <th>Interface</th>
            <th>IPv6 Address</th>
        </tr>
    </thead>
    <tbody>
        <tr>
		<td rowspan=4 align="left">R1</td>
		<td rowspan=2 align="left">Gi0/0</td>
		<td align="left">2001:db8:acad:2::1/64</td>
        </tr>
        <tr>
		<td align="left">fe80::1</td>	
	</tr>
        <tr>
		<td rowspan=2 align="left">Gi0/1</td>
		<td align="left">2001:db8:acad:1::1/64</td>
        </tr>
        <tr>
		<td align="left">fe80::1</td>	
	</tr>
        <tr>
		<td rowspan=4 align="left">R2</td>
		<td rowspan=2 align="left">Gi0/0</td>
		<td align="left">2001:db8:acad:2::2/64</td>
        </tr>
        <tr>
		<td align="left">fe80::2</td>	
	</tr>
        <tr>
		<td rowspan=2 align="left">Gi0/1</td>
		<td align="left">2001:db8:acad:3::1/64</td>
        </tr>
        <tr>
		<td align="left">fe80::1</td>	
	</tr>
	<tr>	    
 		<td align="center">PC-A</td>
	      	<td align="center">NIC</td>
	      	<td align="center">DHCP</td>
        </tr>
        <tr>
      		<td align="center">PC-B</td>
      		<td align="center">NIC</td>
      		<td align="center">DHCP</td>
        </tr>
    </tbody>
</table>

#### VLAN Table

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
		<td align="left">1</td>
		<td align="left">N/A</td>
		<td align="left">S2: F0/18</td>
        </tr>
        <tr>
		<td align="left">100</td>
		<td align="left">Clients</td>
		<td align="left">S1: F0/6</td>
        </tr>
        <tr>
		<td align="left">200</td>
		<td align="left">Management</td>
		<td align="left">S1: VLAN 200 </td>
        </tr>
        <tr>
		<td align="left">999</td>
		<td align="left">Parking_Lot</td>
		<td align="left">S1: F0/1-4, F0/7-24, G0/1-2</td>
        </tr>
        <tr>
		<td align="left">1000</td>
		<td align="left">Native</td>
		<td align="left">N/A</td>
        </tr>
    </tbody>
</table>
