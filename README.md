##  Cisco Three Department Office Network Lab Setup 

### Introduction
Scenario:
You have been sent as the Lead IT Technician to Medtech Services to set up their newly acquired IT devices. The setup includes a multilayer switch that will connect directly to the ISP router and link to three additional switches, each serving one of the following departments: IT, Sales, and HR.

The IT department has the following devices:
- 1 Phone
- 2 PC
- The Sales and HR departments each have:
- 2 PCs
- 1 Phone
As the IT Technician, set up the network using the outline below:
 - 1. Draw the necessary topology, add appropriate decorations, and provide comments.
 - 2. Configure VLANs: name them, assign ports, and set up trunks between switches and the ISP.
 - 3. Create sub-interfaces on the router, bind them to their respective VLAN IDs, and assign IP addresses.
 - 4. Create DHCP pools on the router, specifying the network address, default gateway, and DNS address.
 - 5. Exclude IP address ranges that should not be assigned dynamically.
 - 6. Configure each PC to obtain an IP address via DHCP.
 - 7. Configure the phones to communicate with each other and send data.
  
 ### 1. Draw the necessary topology, add appropriate decorations, and provide comments.

 ### 2. Configure VLANs: name them, assign ports, and set up trunks between switches and the ISP.
- **1. IT Switch:**
- enable
- configure terminal
- hostname IT-Switch
- vlan 10
- name IT
- exit

- vlan 99
- name Management
- exit
interface FastEthernet0/1 Should be truck allowing vlan 10,99
- Thus: from 1-5 will be access port for Vlan 10
- interface range FastEthernet1/1 , FastEthernet2/1 , FastEthernet3/1 , FastEthernet4/1 , FastEthernet5/1  (you can use the range command or do for each port)
- switchport mode access
- switchport access vlan 10
- exit
- ![](https://i.imgur.com/qzdiBIS.png)

- interface FastEthernet2/1   // Phone (Data)
- switchport mode access
- switchport access vlan 10
- switchport voice vlan 10 // Voice VLAN (Same as data here)
- exit
  
- interface FastEthernet0/1 // Trunk to Core
- switchport mode trunk
- switchport trunk allowed vlan 10,99
- exit
- do wr (this will save your configuration)
- ![](https://i.imgur.com/iJJBncv.png)

- **2.Sales Switch:**
- enable
- configure terminal
- hostname Sales-Switch

- vlan 20
- name Sales
- exit

- vlan 99
- name Management
- exit

- interface FastEthernet0/1 // Should be truck allowing vlan 10,99
- Thus: from 1-5 will be access port for Vlan 10
- interface range FastEthernet1/1 , FastEthernet2/1 , FastEthernet3/1 , FastEthernet4/1 , FastEthernet5/1
- switchport mode access
- switchport access vlan 20
- exit

- interface FastEthernet1/1 // Phone (Data)
- switchport mode access
- switchport voice vlan 20 // Voice VLAN (Same as data here)
- exit

- interface FastEthernet0/1   // Trunk to Core
- switchport mode trunk
- switchport trunk allowed vlan 20,99
- exit
- do wr
- ![](https://i.imgur.com/cwlx4q1.png)
 
-  **3. HR Switch:**
- enable
- configure terminal
- hostname HR-Switch

- vlan 30
- name HR
- exit

- vlan 99
- name Management
- exit

- interface FastEthernet0/1 // Should be truck allowing vlan 10,99
- Thus: from 1-5 will be access port for Vlan 10
- interface range FastEthernet1/1 , FastEthernet2/1 , FastEthernet3/1 , FastEthernet4/1 , FastEthernet5/1
- switchport mode access
- switchport access vlan 30
- exit

- interface FastEthernet2/1 // Phone (Data)
- switchport mode access
- switchport voice vlan 30 // Voice VLAN (Same as data here)
- exit

- interface FastEthernet0/1 // Trunk to Core
- switchport mode trunk
- switchport trunk allowed vlan 30,99
- exit
- do wr
- ![](https://i.imgur.com/lDvb0U6.png)

- **4. Multilayer Switch:**
- enable
- configure terminal
- hostname CoreSwitch

- vlan 10  // IT Department
- name IT
- exit

- vlan 20  // Sales Department
- name Sales
- exit

- vlan 30  // HR Department
- name HR
- exit

- vlan 99  // Management
- name Management
- exit
- ![](https://i.imgur.com/Zc1hkEN.png)

- interface range  GigabitEtherne1/0/1-4  // Trunk to Core (you can decide not to use range and configure the interfaces one ofter another)
- switchport mode trunk
- switchport trunk allowed vlan 10,20,30,99
- exit
- do wr
- ![](https://i.imgur.com/qHkZ0RI.png)

### 3. Create sub-interfaces on the router, bind them to their respective VLAN IDs, and assign IP addresses.
-`**Router Subinterfaces:**
- enable
- configure terminal
- hostname ISP-R

- interface FastEthernet0/0.10 // IT
- encapsulation dot1Q 10   // this binds them
- ip address 192.168.1.1 255.255.255.0 //defualt gateway IP for IT
- no shutdown
- exit

- interface FastEthernet0/0.20 // Sales
- encapsulation dot1Q 20
- ip address 192.168.2.1 255.255.255.0  //defualt gateway IP for Sales
- no shutdown
- exit

- interface FastEthernet0/0.30 // HR
- encapsulation dot1Q 30
- ip address 192.168.3.1 255.255.255.0  //defualt gateway IP for HR
- no shutdown
- exit

- interface FastEthernet0/0.99 // Management
- encapsulation dot1Q 99
- ip address 192.168.99.1 255.255.255.0
- no shutdown
- exit
- do wr
- ![](https://i.imgur.com/sC0Bdlc.png)
- ![](https://i.imgur.com/1kLymMd.png)


### 4. Create DHCP pools on the router, specifying the network address, default gateway, and DNS address.
- IT DHCP Pool (On the Router):
- Service dhcp 
- ip dhcp pool IT-Pool
- Network 192.168.1.0 255.255.255.0
- Default-router 192.168.1.1 //Default Gateway
- DNS-Server 192.168.1.1
- exit

- **Sales DHCP Pool:**
- ip dhcp pool Sales-Pool
- Network 192.168.2.0 255.255.255.0
- Default-router 192.168.2.1
- DNS-Server 192.168.2.1
- exit

**HR  DHCP Pool:**
- ip dhcp pool HR-Pool
- Network 192.168.3.0 255.255.255.0
- Default-router 192.168.3.1
- DNS-Server 192.168.3.1
- exit
- do wr
- ![](https://i.imgur.com/WF7bpnm.png)
  
### 5. 5. Exclude IP address ranges that should not be assigned dynamically
- Excluded IP Ranges:
- IT: ip dhcp excluded-address 192.168.1.1 192.168.1.10
- Sales: ip dhcp excluded-address 192.168.2.1 192.168.2.10
- HR: ip dhcp excluded-address 192.168.3.1 192.168.3.10
- ip dhcp excluded-address 192.168.4.1 192.168.4.10
-![](https://i.imgur.com/tfgrbqD.png)

###  6. Configure each PC to obtain an IP address via DHCP.
- Click on the Pc > desktop > IP configuration > Click DHCP
- Each of the Pc will get an Ip address from the DHCP
- snippet of PC0 In IT and Laptop in HR

**Verifies Communication:**
- Pc0 in IT pings laptop1 in Hr
- ping 192.168.1.11
-  Pc02 in Sales pings Pc4 in Hr
-  ping 192.168.3.11
- ![](https://i.imgur.com/N4e1nIh.png)
- ![](https://i.imgur.com/NpDrKUE.png)
- ![](https://i.imgur.com/qEIX3sM.png)
- ![](https://i.imgur.com/AeUkwfZ.png)
- ![](https://i.imgur.com/QBVJTp8.png)
- ![](https://i.imgur.com/vJwLKFs.png)

### Configuration on the ISP Router (CME):
assign Port Fa0/0 an IP address
- enable
- configure terminal
- interface Fa0/0
- ip address 192.168.4.1 255.255.255.0
- no shutdown
- exit
- do wr

**Activate Voice DCHP on the Rourter:**
- Service dhcp
- ip dhcp pool Vioce
- network 192.168.4.0 255.255.255.0
- default-router 192.168.4.1
- option 150 ip 192.168.4.1
- dns-server 192.168.4.1 // Add DNS server
- exit

- **Call Manager Telephony Service:**
- telephony-service
- max-ephones 8
- max-dn 8
- ip source-address 192.168.4.1 port 2000
- auto assign 1 to 8
- exit

- ephone-dn 1
- number 1001
- exit

- ephone-dn 2
- number 2001
- exit

- ephone-dn 3
- number 3001
- exit
- ![](https://i.imgur.com/EJCpPKi.png)

- ephone 1
- mac-address 0005.5e74.ee35
- type 7960
- button 1:1 // Associate with ephone-dn 1
- exit

- ephone 2
- mac-address 0050.0f92.2d00
- type 7960
- button 1:2 // Associate with ephone-dn 2
- exit

- ephone 3
- mac-address 0030.a3e7.5cb3
- type 7960
- button 1:3 // Associate with ephone-dn 3
- exit

## Assign a Voice VLAN Router Fa0/0
- voice vlan 10 // IT Phone
- voice vlan 20 // Sales Phone
- voice vlan 30 // HR Phone

- voice service voip
- allow-connections sip to sip
- sip
- registrar server expires 600
- exit
- exit
- exit
- write memory

