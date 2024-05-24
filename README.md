# home-network-vlan-setup
## Overview
This documentation highlights the steps that I took to setup VLANs on my home network to segment different types of devices for improved security and management. The setup will include a OPNsense router, a Netgear GS108E V3 managed switch, and a TP-Link Archer AX1500 router running in AP mode.

## Equipment
  -**OPNSense Router**
  -**Netgear GS108E V3 Managed Switch**
  -**TP-Link Archer AX1500 Router running in AP mode since I don't own proper AP**

## Devices
-**Desktop computer** connected to port 1 on the switch via ethernet
-**HPE Proliant DL360P G8 Server running UNRAID OS**
-UNRAID connected to port 2 on the switch via ethernet
-iLO 4 connected to port 3 on the switch via ethernet
-**Wireless Devices:**
  -iPhone
  -MacBook Air
  -Amazon Echo Dot

##VLAN Configuration Plan

### VLANs to Create
  -**VLAN 10:** Trusted Devices (Desktop, MacBook Air)
  -**VLAN 20:** Servers (UNRAID, iLO 4)
  -**VLAN 30:** IoT Devics (iPhone, Amazon Echo Dot)

## Step-by-Step Configuration

### 1. OPNSense Router Configuration

**Create VLAN Interfaces:**
  1. Log into OPNsense
  2. Go to **Interfaces > Other Types > VLAN**
  3. Click **ADD** to create VLANs:
     - **VLAN 10:** Set Parent Interface to the interface connected to the switch (in my case `igb3`), VLAN Tag to 10
     - **VLAN 20:** Set Parent Interface to the interface connected to the switch (in my case `igb3`), VLAN Tag to 20
     - **VLAN 30:** Set Parent Interface to the interface connected to the switch (in my case `igb3`), VLAN Tag to 30

  **Assign VLAN Interfaces:**
    1. Go to **Interfaces > Assignments**.
    2. Assign each VLAN to a new interface: 
        - Enable each new interface by clicking on its name (in my case it was `opt1`), checking **Enable Interface**, and renaming it to the proper VLAN names (VLAN 10, VLAN 20, VLAN 30).

  **Configure VLAN Interfaces:**
    1. Go to **Interfaces > [VLAN10]**:
      - Check **Enable Interface**
      - Set **IPv4 Configuration Type** to **Static IPv4**
      - Set IP address (I set it to 192.168.10.1/24 to keep everything the same)
      - Enable **DHCP** and configure DHCP settings. 
    2. Repeat the above steps for VLAN 20, 30, using IP ranges `192.168.20.1/24` and `192.168.30.1/24`


  **Firewall Rules:**
    1. Go to **firewall > Rules**.
    2. For each VLAN interface, create rules that would: 
      - Allow intra-VLAN traffic
      - Allow necessary inter-VLAN traffic. 
      - Block or restrict traffic between VLANs
    3. Example rule set for VLAN10 (Trusted Devices):
        - Allow traffic from VLAN10 net to any (outbound).
        - Block traffic from VLAN10 to VLAN20 and VLAN30 except for specific riles (allowing access to specific services if you need to)

  ### 2. Netgear GS108E V3 Switch Configuration

  **Access the Switch Configuration:**
    1. Connect your PC to the switch and access the Web UI using its IP address (in my case, I had to factory reset it and set my computer to have a manual IP 
