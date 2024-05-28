Home Network VLAN Configuration Guide

This guide details the steps to segment a home network using VLANs with the following hardware setup:

	•	Router: OPNsense
	•	Switch: Netgear GS108E V3
	•	Access Point: TP-Link Archer AX1500 (AP Mode)
	•	Devices:
	•	Windows 11 Desktop (Wired)
	•	HPE Proliant DL360P G8 Server running UNRAID (Wired, with iLO)
	•	M1 MacBook Air (Wireless)
	•	iRobot Roomba (Wireless)
	•	iPhone (Wireless)
	•	Amazon Alexa (Wireless)
	•	Smart TV (Wireless)

VLAN Plan

	1.	VLAN 10 - Management: iLO and OPNsense management interfaces.
	2.	VLAN 20 - Servers: UNRAID server.
	3.	VLAN 30 - Trusted Devices: Windows 11 desktop and personal devices.
	4.	VLAN 40 - IoT Devices: iRobot Roomba, Smart TV, Amazon Alexa.
	5.	VLAN 50 - Guest WiFi: Guest devices like iPhone and MacBook Air.

Step-by-Step Configuration

Step 1: Configure VLANs on OPNsense

Access OPNsense GUI

	1.	Open a web browser and navigate to your OPNsense router’s IP address (default is usually 192.168.1.1).
	2.	Log in with your admin username and password.

Add VLANs

	1.	Navigate to Interfaces > Other Types > VLAN.
	2.	Click the + button to add a new VLAN.
	3.	Create VLANs with the following details:

VLAN ID	Description	Parent Interface
10	Management	LAN
20	Servers	LAN
30	Trusted Devices	LAN
40	IoT Devices	LAN
50	Guest WiFi	LAN



Assign Interfaces

	1.	Navigate to Interfaces > Assignments.
	2.	You will see the newly created VLAN interfaces. Click the + button to assign each VLAN.
	3.	Configure each VLAN interface:
	•	Enable the interface.
	•	IPv4 Configuration Type: Static IPv4
	•	IP Address:
	•	VLAN 10: 192.168.10.1/24
	•	VLAN 20: 192.168.20.1/24
	•	VLAN 30: 192.168.30.1/24
	•	VLAN 40: 192.168.40.1/24
	•	VLAN 50: 192.168.50.1/24
	•	Save and apply changes.

Configure DHCP for VLANs

	1.	Navigate to Services > DHCPv4.
	2.	Create a DHCP scope for each VLAN:
	•	VLAN 10:
	•	Interface: VLAN 10
	•	Enable DHCP Server
	•	Range: 192.168.10.10 to 192.168.10.100
	•	VLAN 20:
	•	Interface: VLAN 20
	•	Enable DHCP Server
	•	Range: 192.168.20.10 to 192.168.20.100
	•	VLAN 30:
	•	Interface: VLAN 30
	•	Enable DHCP Server
	•	Range: 192.168.30.10 to 192.168.30.100
	•	VLAN 40:
	•	Interface: VLAN 40
	•	Enable DHCP Server
	•	Range: 192.168.40.10 to 192.168.40.100
	•	VLAN 50:
	•	Interface: VLAN 50
	•	Enable DHCP Server
	•	Range: 192.168.50.10 to 192.168.50.100
	3.	Save the changes.

Configure Firewall Rules

	1.	Navigate to Firewall > Rules.
	2.	Select each VLAN interface and create rules as needed:
	•	For basic connectivity within the VLAN, allow all traffic.
	•	Create rules to allow or block traffic between VLANs based on your requirements.

Step 2: Configure VLANs on Netgear GS108E V3 Switch

Access the Netgear Switch

	1.	Download and install the Netgear ProSAFE Plus Configuration Utility from Netgear’s website.
	2.	Open the utility and connect to your switch.

Create VLANs

	1.	Navigate to VLAN > 802.1Q > Advanced.
	2.	Click Add VLAN.
	3.	Create VLANs with the following details:

VLAN ID	VLAN Name
10	Management
20	Servers
30	Trusted Devices
40	IoT Devices
50	Guest WiFi



Assign VLANs to Ports

	1.	Navigate to VLAN > 802.1Q > VLAN Membership.
	2.	Configure the ports for each VLAN:
	•	VLAN 10:
	•	Port 4 (Connected to OPNsense): Tagged
	•	Port 1 (iLO): Untagged
	•	VLAN 20:
	•	Port 4 (Tagged), Port 2 (Untagged)
	•	VLAN 30:
	•	Port 4 (Tagged), Port 5 (Untagged)
	•	VLAN 40:
	•	Port 4 (Tagged), Port 3 (Untagged)
	•	VLAN 50:
	•	Port 4 (Tagged), Port 3 (Untagged)

Set PVID for Untagged Ports

	1.	Navigate to VLAN > PVID.
	2.	Assign the PVID for each untagged port:
	•	Port 1: PVID 10
	•	Port 2: PVID 20
	•	Port 5: PVID 30
	•	Port 3: PVID 40 (IoT) or 50 (Guest)

Step 3: Connect Devices

Wired Devices

	1.	Windows 11 Desktop: Connect to port 5.
	2.	UNRAID server (main interface): Connect to port 2.
	3.	iLO: Connect to port 1.

Wireless Devices

	1.	TP-Link Archer AX1500 (AP Mode): Connect to port 3 of the Netgear switch.
	•	Note: The TP-Link router in AP mode cannot tag VLANs, so it will use the PVID assigned to port 3. Set the PVID to either VLAN 40 (IoT) or VLAN 50 (Guest) based on the primary use.

Step 4: Verify Connectivity

	1.	Check IP Assignments: Ensure each device receives an IP address from the correct VLAN DHCP scope.
	2.	Test Connectivity: Verify network connectivity and inter-VLAN communication based on firewall rules.

Summary

	•	VLAN 10 (Management): iLO (Port 1), OPNsense management interface.
	•	VLAN 20 (Servers): UNRAID (Port 2).
	•	VLAN 30 (Trusted Devices): Windows 11 Desktop (Port 5).
	•	VLAN 40 (IoT Devices): IoT devices via TP-Link AP (Port 3 PVID 40).
	•	VLAN 50 (Guest WiFi): Guest devices via TP-Link AP (Port 3 PVID 50 when needed).
