# Лабороторная работа на тему:  
## Implement DHCP  
#### Топология  

![Alt text](https://github.com/bislogin/otus/blob/main/labs/lab02/topology.png)  

#### Addressing Table

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
			      <td align="left"> </td>
			      <td align="left"> </td>
        </tr>
      	<tr>
			      <td align="left">Gi0/1.200	</td>
			      <td align="left"> </td>
			      <td align="left"> </td>
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
			      <td align="left"> </td>
			      <td align="left"> </td>
        </tr>
        <tr>
			      <td align="left">S1</td>
	      		<td align="left">VLAN 200</td>
	      		<td align="left"> </td>
		      	<td align="left"> </td>
            <td align="left"> </td>
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
