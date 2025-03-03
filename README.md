##  Cisco Three Department Office Network Lab Setup

### Introduction
Scenario:
You have been sent as the Lead IT Technician to Medtech Services to set up their newly acquired IT devices. The setup includes a multilayer switch that will connect directly to the ISP router and link to three additional switches, each serving one of the following departments: IT, Sales, and HR.

Each of the  departments have the following devices:
- 1 Phone
- 2 PC
As the IT Technician, set up the network using the outline below:
 - 1. Draw the necessary topology, add appropriate decorations, and provide comments.
 - 2. Configure VLANs: name them, assign ports, and set up trunks between switches and the ISP.
 - 3. Create sub-interfaces on the router, bind them to their respective VLAN IDs, and assign IP addresses.
 - 4. Create DHCP pools on the router, specifying the network address, default gateway, and DNS address.
 - 5. Exclude IP address ranges that should not be assigned dynamically.
 - 6. Configure each PC to obtain an IP address via DHCP.
 - 7. Configure the phones to communicate with each other and send data.
 - 8. Verifies Communication:
   - a. Establish a Call from Hr to IT department
   - b. Pc0 in IT pings laptop1 in Hr
   - c. Pc02 in Sales pings Pc4 in Hr
   - d. Show the TCPP activities captured by the phone
   - Verifies Communication:
  
 ### 1. Draw the necessary topology, add appropriate decorations, and provide comments.

 - ![](https://i.imgur.com/VuURK0G.png)

 ### 2. Configure VLANs: name them, assign ports, and set up trunks between switches and the ISP.
- **1. IT Switch:**
- enable
- configure terminal
- hostname IT-Switch

- vlan 4
- name Voice
- exit

- vlan 10
- name IT
- exit

- vlan 99
- name Management
- exit

interface FastEthernet0/1 //Should be truck allowing vlan 4,10,99
- interface range FastEthernet1/1 - FastEthernet5/1
- switchport mode access
- switchport access vlan 10
- exit

- interface FastEthernet2/1   // Phone port
- switchport mode access
- switchport access vlan 10
- switchport voice vlan 4
- exit

- interface FastEthernet0/1  //Trunk to Core
- switchport mode trunk
- switchport trunk allowed vlan 4,10,99
- exit
- do wr
- ![](https://i.imgur.com/7HQVArs.png)

- **2.Sales Switch:**
- enable
- configure terminal
- hostname Sales-Switch

- vlan 4
- name Voice
- exit

- vlan 20
- name Sales
- exit

- vlan 99
- name Management
- exit

- interface range FastEthernet1/1 - FastEthernet5/1
- switchport mode access
- switchport access vlan 20
- exit

- interface FastEthernet1/1  // Phone port
- switchport mode access
- switchport access vlan 20
- switchport voice vlan 4
- exit

- interface FastEthernet0/1  // Trunk to Core
- switchport mode trunk
- switchport trunk allowed vlan 4,20,99
- exit
- do wr
- ![](https://i.imgur.com/Y5Y9gU0.png)

-  **3. HR Switch:**
- enable
- configure terminal
- hostname HR-Switch

- vlan 4
- name Voice
- exit

- vlan 30
- name HR
- exit

- vlan 99
- name Management
- exit

- interface range FastEthernet1/1 - FastEthernet5/1
- switchport mode access
- switchport access vlan 30
- exit

- interface FastEthernet2/1 // Phone port
- switchport mode access
- switchport access vlan 30
- switchport voice vlan 4
- exit

- interface FastEthernet0/1  // Trunk to Core
- switchport mode trunk
- switchport trunk allowed vlan 4,30,99
- exit
- do wr

- ![](https://i.imgur.com/xg8kYCv.png)

- **4. Multilayer Switch:**
- enable
- configure terminal
- hostname CoreSwitch

- vlan 4
- name Voice
- exit

- vlan 10
- name IT
- exit

- vlan 20
- name Sales
- exit

- vlan 30
- name HR
- exit

- vlan 99
- name Management
- exit

- interface range GigabitEthernet1/0/1-4  // Trunk to other switches
- switchport mode trunk
- switchport trunk allowed vlan 4,10,20,30,99
- exit
- ip routing  # Enable routing on the switch
- exit
- do wr
- ![](https://i.imgur.com/D8KkOT1.png)
- ![](https://i.imgur.com/OpSngyi.png)

### 3. Create sub-interfaces on the router, bind them to their respective VLAN IDs, and assign IP addresses.
-`**Router Subinterfaces:**
- enable
- configure terminal
- hostname ISP-R

- interface FastEthernet0/0.4  // Voice VLAN
- encapsulation dot1Q 4
- ip address 192.168.4.1 255.255.255.0
- no shutdown
- exit

- interface FastEthernet0/0.10  // IT
- encapsulation dot1Q 10
- ip address 192.168.1.1 255.255.255.0
- no shutdown
- exit

- interface FastEthernet0/0.20  // Sales
- encapsulation dot1Q 20
- ip address 192.168.2.1 255.255.255.0
- no shutdown
- exit

- interface FastEthernet0/0.30  // HR
- encapsulation dot1Q 30
- ip address 192.168.3.1 255.255.255.0
- no shutdown
- exit

- interface FastEthernet0/0.99  # Management
- encapsulation dot1Q 99
- ip address 192.168.99.1 255.255.255.0
- no shutdown
- exit
- do wr
- ![](https://i.imgur.com/eooN8wH.png)
  
### 4. Create DHCP pools on the router, specifying the network address, default gateway, and DNS address.
- enable
- configure terminal
- service dhcp 

- ip dhcp pool IT-Pool
- network 192.168.1.0 255.255.255.0
- default-router 192.168.1.1
- dns-server 192.168.1.1
- exit

- ip dhcp pool Sales-Pool
- network 192.168.2.0 255.255.255.0
- default-router 192.168.2.1
- dns-server 192.168.2.1
- exit

- ip dhcp pool HR-Pool
- network 192.168.3.0 255.255.255.0
- default-router 192.168.3.1
- dns-server 192.168.3.1
- exit
- do wr
- ![](https://i.imgur.com/WF7bpnm.png)
  
### 5. Exclude IP address ranges that should not be assigned dynamically
- Excluded IP Ranges:
- ip dhcp excluded-address 192.168.1.1 192.168.1.10 // IT
- ip dhcp excluded-address 192.168.2.1 192.168.2.10 // Sales
- ip dhcp excluded-address 192.168.3.1 192.168.3.10 // HR
-![](https://i.imgur.com/tfgrbqD.png)

###  6. Configure each PC to obtain an IP address via DHCP.
- Click on the Pc > desktop > IP configuration > Click DHCP
- Each of the Pc will get an Ip address from the DHCP
- snippet of PC0 In IT and Laptop in HR
- ![](https://i.imgur.com/N4e1nIh.png)
- ![](https://i.imgur.com/NpDrKUE.png)
- ![](https://i.imgur.com/qEIX3sM.png)
- ![](https://i.imgur.com/AeUkwfZ.png)

###  7. Configure the phones to communicate with each other and send data
- Configure Cisco Call Manager Express (CME)
- Enable DHCP for Phones
 - enable
 - configure terminal

- service dhcp 
- ip dhcp pool Voice
- network 192.168.4.0 255.255.255.0
- default-router 192.168.4.1
- option 150 ip 192.168.4.1  // TFTP Server for Phones
- dns-server 192.168.4.1
- exit

- interface FastEthernet0/0
- ip address 192.168.4.1 255.255.255.0
- no shutdown
- exit
- do wr
- ![](https://i.imgur.com/gleBd30.png)

## Set Up Call Manager Telephony Service
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

- ephone 1
- mac-address 0005.5e74.ee35
- type 7960
- button 1:1
- exit

- ephone 2
- mac-address 0050.0f92.2d00
- type 7960
- button 1:2
- exit

- ephone 3
- mac-address 0030.a3e7.5cb3
- type 7960
- button 1:3
- exit
- do wr

### 8. Verifies Communication:
   - a. Extablish a Call from Hr to IT department
   - ![](https://i.imgur.com/FaJ7Tyc.png)
   - ![](https://i.imgur.com/lZG8xKZ.png)
  
   - b. Pc0 in IT pings laptop1 in Hr
   - ping 192.168.1.11
   - ![](https://i.imgur.com/QBVJTp8.png)
 
   - c. Pc02 in Sales pings Pc4 in Hr
   - ping 192.168.3.11
   - ![](https://i.imgur.com/vJwLKFs.png)
   
   - d. Show the TCP activitives captured by the phone
   - ![](https://i.imgur.com/TIrLuWC.png)

Conclution:
Setting up a Cisco Three Department Office Network Lab is essential for an IT technician as it provides hands-on experience in designing, configuring, and troubleshooting enterprise-level networks. This setup demonstrates fundamental networking skills, including VLAN segmentation, IP addressing, DHCP configuration, trunking, inter-VLAN routing, and VoIP implementation. Mastering these concepts is crucial for managing real-world IT infrastructure, ensuring network security, reliability, and efficient resource allocation.



