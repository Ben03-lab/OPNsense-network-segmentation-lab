Overview
This project demonstrates the reconfiguration of a multi‑segment network to support organizational restructuring. The environment uses VLAN segmentation, Layer 2/Layer 3 routing, and an OPNsense firewall to enforce perimeter security and control traffic between internal networks.
The lab highlights real‑world skills in routing, switching, firewall rule creation, and troubleshooting return‑path issues.

I. Network Reconfiguration
A. Network Diagram
<img width="958" height="887" alt="Screenshot 2026-02-28 114127" src="https://github.com/user-attachments/assets/2e8e390d-3880-47d3-804d-e5895ba3dd8c" />
The network was redesigned to segment traffic into multiple VLANs routed through a central ServerRouter.
The OPNsense firewall provides perimeter protection, while internal VLANs isolate departments and servers.
B. Port Assignment & VLAN Configuration
<img width="689" height="485" alt="Screenshot 2026-02-28 102518" src="https://github.com/user-attachments/assets/12882a52-8b38-4dfb-9d5a-5b90b0aadb5d" />
<img width="691" height="485" alt="Screenshot 2026-02-28 102606" src="https://github.com/user-attachments/assets/3a2b1225-79f4-48e2-8169-d771148e6fc7" />
<img width="693" height="484" alt="Screenshot 2026-02-28 102657" src="https://github.com/user-attachments/assets/fd44ac30-e3d8-4578-9a9e-78b393469f31" />
<img width="691" height="482" alt="Screenshot 2026-02-28 100527" src="https://github.com/user-attachments/assets/72e423ef-5d62-47e0-b959-e4f2f5412975" />
Switch Configuration Summary
The switches were configured using:
• 	Access mode for end‑device ports
• 	dot1q trunk mode for uplinks
This allowed VLAN tags to pass between switches and routers while keeping end‑device ports untagged.
ServerSwitch Configuration
• 	Port 1 → VLAN 4 (access)
• 	Port 2 → VLAN 4 (access)
• 	Port 0 → dot1q trunk (uplink to BackboneSwitch)
BackboneSwitch Configuration
• 	Trunk ports configured using dot1q
• 	VLANs passed between ServerRouter and Firewall
These configurations ensured proper Layer 2 segmentation and enabled the ServerRouter to route traffic between VLANs.

II. Traffic Flow Configuration
Firewall Rules Implemented
A. 	Allow HTTP (80) from WAN → Web Server
B. 	Allow HTTPS (443) from WAN → Web Server
C. 	Block HTTP (80) from WAN → all other systems
D. 	Block HTTPS (443) from WAN → all other systems
<img width="722" height="458" alt="Screenshot 2026-02-28 133952" src="https://github.com/user-attachments/assets/54641bb4-7acb-4bde-9b10-cecdda5e2249" />
<img width="665" height="418" alt="Screenshot 2026-02-28 163440" src="https://github.com/user-attachments/assets/91d2a33c-1d3b-44b2-bb37-a9bd19fd1c62" />
<img width="658" height="417" alt="Screenshot 2026-02-28 163510" src="https://github.com/user-attachments/assets/fe164bef-c3d5-4b4c-8d58-a40d92e06622" />
<img width="658" height="415" alt="Screenshot 2026-02-28 163536" src="https://github.com/user-attachments/assets/078f4b82-315c-47a8-bfb5-fb5904c2d625" />
Explanation of Attempt & Issue Encountered
All VLANs, switch ports, and router interfaces were configured correctly:
• 	The ServerRouter successfully routed traffic between VLAN 4 (Server VLAN) and the backbone network.
• 	The AuthServer could reach the ServerRouter on both VLAN and backbone interfaces.
• 	The ServerRouter could reach the firewall at 192.168.1.1.
However, the firewall could not return traffic to the 192.168.4.0/24 network.
This prevented access to the firewall’s web GUI, which was required to configure firewall rules.
Attempted Fix
A static route was added through the OPNsense console:
<img width="722" height="458" alt="Screenshot 2026-02-28 133952" src="https://github.com/user-attachments/assets/930482ad-2415-426a-86dc-c616f9e292bc" />
Root Cause
OPNsense requires a gateway object to be created through the GUI before static routes become active.
Because the GUI was unreachable, the gateway object could not be created, and the firewall continued to drop return traffic.
Ping Test Confirmation
A ping test from the firewall console confirmed the issue:
(Insert ping failure screenshot here)
<img width="723" height="459" alt="Screenshot 2026-02-28 161619" src="https://github.com/user-attachments/assets/31d42a50-6c4a-443b-9e08-acccf4c6a4f0" />
This demonstrated that the firewall could not reach the Server VLAN, preventing completion of the firewall rule configuration.

III. 🛡️ Organizational Security Strategy
A. How Security Posture Improved
The restructuring significantly improves the organization’s security posture through:
• 	Network segmentation
VLANs isolate departments and servers, reducing lateral movement opportunities.
• 	Controlled routing
All inter‑VLAN traffic passes through the ServerRouter for monitoring and filtering.
• 	Perimeter protection
OPNsense provides a single controlled entry point for WAN traffic.
• 	Reduced broadcast domains
Each VLAN operates independently, improving performance and limiting unnecessary traffic.
B. Impact on the CIA Triad
Confidentiality
VLAN segmentation isolates sensitive systems (authentication servers, web servers) from general user traffic.
Integrity
Routing through the ServerRouter ensures only authorized traffic reaches critical systems.
Firewall rules (once implemented) enforce strict control over inbound WAN traffic.
Availability
Segmenting the network reduces congestion and prevents a single broadcast domain from overwhelming the entire network.

Conclusion
The network reconfiguration, VLAN assignments, and routing were successfully implemented.
The only unresolved issue was the firewall’s inability to return traffic to the Server VLAN due to a missing gateway object, which prevented GUI access and completion of firewall rule configuration.
Despite this, the project demonstrates strong understanding of:
• 	Network design
• 	VLAN segmentation
• 	Routing logic
• 	Firewall behavior
• 	Troubleshooting methodology
This lab reflects real‑world challenges faced in network security environments.

