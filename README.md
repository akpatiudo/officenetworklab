##  Cisco Office Network Lab Setup (Two Floors, Two Servers, Two VoIP Phones, Two Computers, Router, Switch, and Cloud TP)

### Introduction
**1. What We're Building (The Big Picture):**

Imagine a small office with two floors. We need to connect everyone to the internet, allow them to make phone calls over the network (VoIP), have a server for company files (DHCP), and a website for employees (Web Server).  We'll use VLANs to keep different types of traffic separate (like phone calls and computer data).

**2. The Equipment (Our Tools)**
This lab will simulate an office network with the following components:

-  Router (R1): The "boss" of the network. It connects us to the internet (CloudTP) and directs traffic between different parts of our office.
-  Switch (S1): Like a multi-socket extension cord for network cables. It connects all the devices on the network.
-  DHCP Server: A computer that automatically gives out IP addresses to devices so they can connect to the network.
-  Web Server: A computer that hosts a website.
-  CloudTP: Simulates the internet.
-  VoIP Phones: Phones that use the network to make calls.
-  Computers (PC1 & PC2): Regular office computers.

  **IP Address Plan:**
  ![](https://i.imgur.com/y7FV8u1.png)

 **3.  Setting Up the Network (Connecting the Pieces):**
-  Router to CloudTP: Connect a cable from the Router (Serial2/0) to the CloudTP. This is our internet connection.
-  Important: The CloudTP needs to be configured to act as an internet gateway. This usually involves setting up a connection to a simulated ISP within the CloudTP                configuration.
-  Router to Switch: Connect a cable from the Router (FastEthernet0/0) to the Switch (FastEthernet6/1). This connects the office network to the "boss" (router).
-  **Switch to Devices:**
  -  Connect the DHCP Server to the Switch (e.g., FastEthernet0/1).
  -  Connect the Web Server to the Switch (e.g., FastEthernet1/1).
  -  Connect VoIP Phone 1 to the Switch (e.g., FastEthernet3/1).
  -  Connect VoIP Phone 2 to the Switch (e.g., FastEthernet8/1).
  -  Connect PC1 to the Switch (e.g., FastEthernet2/1).
  -  Connect PC2 to the Switch (e.g., FastEthernet7/1).

**4. Dividing the Network (VLANs):**
-  We'll use VLANs to separate traffic. Think of them like different lanes on a highway.
-  VLAN 10 (Data): For computers (PC1 & PC2).
-  VLAN 20 (Voice): For VoIP phones.
-  VLAN 30 (Servers): For the DHCP and Web servers.
-  VLAN 99 (Management): For the router and switch.

**5. Configuring the Router (R1 - The Boss):**
-  Connect to Router: Connect a console cable to the router and use a computer with terminal software (like PuTTY) to access it.
-  Basic Settings:
-  enable
-  configure terminal
-  hostname R1
-  Subinterfaces (for VLANs): We create subinterfaces on the router's FastEthernett0/0 port, one for each VLAN. This is how the router talks to each VLAN.
  -  interface FastEthernett0/0.10
  -  encapsulation dot1Q 10  (Tells the router this is VLAN 10 traffic)
  -  ip address 192.168.10.1 255.255.255.0 (IP address for VLAN 10)
  -  no shutdown
  -  exit
 (Repeat for VLAN 20, 30, and 99, changing IP addresses accordingly)

 **VLAN 20 - Phones**
-  interface FastEthernet0/0.20
-  encapsulation dot1Q 20
-  ip address 192.168.20.1 255.255.255.0
-  no shutdown
 -  exit

**VLAN 30 - Servers (DHCP & Web)**
-  interface FastEthernet0/0.30
 -  encapsulation dot1Q 30
 -  ip address 192.168.30.1 255.255.255.0
 -  no shutdown
 -  exit

**VLAN 99 - Management (Switch Management VLAN)**
-  interface FastEthernet0/0.99
 -  encapsulation dot1Q 99
 -  ip address 192.168.99.1 255.255.255.0
 -  no shutdown
 -  exit

**Default Route (to the internet):**

ip route 0.0.0.0 0.0.0.0 203.0.113.1 (Replace 203.0.113.1 with the CloudTP's IP address)

**6. Configuring the Switch (S1 - The Connector):**
Connect to Switch: Connect a console cable to the switch and use terminal software. (in real world)
1. Create VLANs on the Switch (VLAN Configuration)
-  enable
-  configure terminal

-  vlan 10
-  #name DATA
-  exit

-  vlan 20
-  name VOICE
-  exit

-  vlan 30
-  name SERVERS
-  exit

-  vlan 99
-  name MANAGEMENT
-  exit

###  2. Assign VLANs to Access Ports (PCs, Phones, and Servers)
-  ![](https://i.imgur.com/qvmMmcl.png)

**PC Ports (Fa2/1 & Fa7/1) - VLAN 10**

-  interface FastEthernet2/1
-  switchport mode access
-  switchport access vlan 10
-  exit

-  interface FastEthernet7/1
-  switchport mode access
-  switchport access vlan 10
-  exit

**VoIP Phones (Fa3/1 & Fa8/1) - VLAN 20**
-  VoIP Phones need both Data VLAN (10) and Voice VLAN (20).
  -  interface FastEthernet3/1
  -  switchport mode access
  -  switchport access vlan 10
  -  switchport voice vlan 20
  -  exit

  -  interface FastEthernet8/1
  -  switchport mode access
  -  switchport access vlan 10
  -  switchport voice vlan 20
  -  exit

**Server Ports (Fa0/1 & Fa1/1) - VLAN 30**
-  interface FastEthernet0/1
-  switchport mode access
-  switchport access vlan 30
-  exit

-  interface FastEthernet1/1
-  switchport mode access
-  switchport access vlan 30
-  exit

**3  Trunk Port (to Router):**
The port connecting the switch to the router needs to be a "trunk" port, which can carry traffic for multiple VLANs.
-  interface  FastEthernet6/1
-  switchport mode trunk
-  switchport trunk allowed vlan 10,20,30,99
-  exit

### 7. Configuring the DHCP Server:
You can Set this up in two ways:
1  Access DHCP Server: Go to the DHCP Server's desktop and IP configuration. 
-  Set a static IP address: 192.168.30.10
-  subnet mask 255.255.255.0,
-  default gateway 192.168.30.1,
-  DNS server 8.8.8.8.
-  DHCP Configuration: Go to the "Services" tab and then "DHCP." Turn the DHCP service "On."

**Pool for VLAN 10 (Data):**
-  Default Gateway: 192.168.10.1
-  DNS Server: 8.8.8.8
-  Start IP Address: 192.168.10.11
-  Subnet Mask: 255.255.255.0
-  Maximum Number of Users: (Set as needed)

-  Pool for VLAN 20 (Voice):
-  Default Gateway: 192.168.20.1
-  DNS Server: 8.8.8.8
-  Start IP Address: 192.168.20.11
-  Subnet Mask: 255.255.255.0
-  Maximum Number of Users: (Set as needed)

**Configuring the DHCP Server: Method Two**
-  Enable DHCP for PCs
-  ip dhcp excluded-address 192.168.10.1 192.168.10.10
-  ip dhcp pool DATA-NET
-  network 192.168.10.0 255.255.255.0
-  default-router 192.168.10.1
-  dns-server 8.8.8.8
-  exit

**Enable DHCP for VoIP Phones**
-  ip dhcp excluded-address 192.168.20.1 192.168.20.10
-  ip dhcp pool VOICE-NET
-  network 192.168.20.0 255.255.255.0
-  default-router 192.168.20.1
-  option 150 ip 192.168.20.1
-  exit

###  8. Configuring the Web Server:
-  Access Web Server: Go to the Web Server's desktop and IP configuration.
-  Set a static IP address: 192.168.30.20
-  subnet mask 255.255.255.0
-  default gateway 192.168.30.1, DNS server 8.8.8.8.
-  Web Service: Go to the "Services" tab and then "HTTP." Turn the HTTP service "On."


**Configuring the Web Server: Method Two**
-  Enable Web Service
-  enable
-  configure terminal
-  interface Vlan30
-  ip address 192.168.30.20 255.255.255.0
-  no shutdown
-  exit

### 9. Configuring VoIP Phones:
Connect the phones to the correct switch ports (configured for both VLAN 10 and 20).

The phones should automatically get IP addresses from the DHCP server (VLAN 20). They will also need to be configured with the correct settings for your VoIP provider (which is beyond the scope of this basic network setup).

### 10. Configuring Computers (PC1 & PC2):
-  Set the computers to obtain IP addresses automatically (DHCP).
-  -  They should get IP addresses from the DHCP server (VLAN 10).

###  11. Testing:
-  Ping: From the computers,
-  pinging the router (192.168.10.1 or 192.168.20.1)
- the web server (192.168.30.20)
- and an external IP address (like 8.8.8.8).
-  Web Browser: From the computers, open a web browser and try accessing the web server (http://192.168.30.20).
-  VoIP Phones: Try making calls between the VoIP phones.

**Check VLANs:**
-  show vlan brief

**Check DHCP:**
show ip dhcp binding

###  Configure the CloudTP as an ISP
1. Assign an IP Address to the Cloud Interface
  -  Open the CloudTP device.
  -  Click on the "Config" tab.
  -  Select "Serial0" (the interface connecting to the router).
  - Set the following configurations:
    -  Port Status: On
    -  Encapsulation: PPP
    -  IP Address: 203.0.113.1
    -  Subnet Mask: 255.255.255.0

### Configure the Router’s Serial2/0 Interface
On your router, enter configuration mode:
-  enable
-   configure terminal
-  Enter interface configuration mode for Serial2/0:
    - interface Serial2/0
    -  ip address 203.0.113.2 255.255.255.0
    -  encapsulation ppp
    -  no shutdown
    -  exit
**Verify the connection:**
-  Router# show ip interface brief
-  Ensure the Serial2/0 interface is UP.

**Step 3: Set the Default Route to the Cloud**
To allow the router to forward all external traffic to CloudTP (ISP), configure a default static route:
-  Router(config)# ip route 0.0.0.0 0.0.0.0 203.0.113.1
-  This means:
-  Any traffic with an unknown destination (i.e., the internet) will be sent to 203.0.113.1 (CloudTP).

###  Step 4: Enable NAT (Optional, for LAN to Internet)
If your LAN (e.g., VLAN 10, VLAN 20) needs internet access, configure NAT (Network Address Translation) on the router.
1. Enable NAT on the Routert
-   access-list 1 permit 192.168.1.0 0.0.0.255
-  access-list 1 permit 192.168.30.0 0.0.0.255

-  interface Serial2/0
-  ip nat outside
-  exit

-   interface FastEthernet6/
-   ip nat inside
-    exit

ip nat inside source list 1 interface Serial2/0 overload

**2. Test Internet Connectivity**
From a PC in VLAN 10, test by pinging Google's DNS (8.8.8.8):

PC> ping 8.8.8.8
If the ping works, your CloudTP is successfully simulating an ISP!

**Final Verification**
-  show ip route
You should see:
S*   0.0.0.0/0 [1/0] via 203.0.113.1

**Check NAT Translations:**
-  Router# show ip nat translations
-  Check Serial Interface Status:
    -  show interfaces Serial2/0
    -  Ensure it is UP/UP.

Now your CloudTP is acting as an ISP, and your router can reach the internet! 
### Conclusion
Your Cisco office lab is now set up with: 
- Two Floors
- Two Servers (DHCP & Web)
- Two VoIP Phones
- Two Computers
- Router & Switch with VLANs
- CloudTP (Internet) Connection

This lab covers networking, VoIP, DHCP, web hosting, and security best practices
Final Thoughts
CloudTP's Serial0 acts as the ISP gateway (203.0.113.1).
Router's Serial2/0 is the WAN interface (203.0.113.2).
FastEthernet6/1 (or the correct LAN interface) connects your local devices.
NAT is configured to allow multiple LAN devices to access the internet.
