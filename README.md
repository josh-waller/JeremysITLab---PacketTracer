Here’s your content reformatted with clear GitHub‑friendly breaks (Markdown style). Each section is separated with horizontal rules (---) so it looks clean and easy to navigate in a README or documentation file.

Enterprise Network Implementation Summary
Implemented a full two‑office enterprise network with VLANs, EtherChannels, routing, services, security, IPv6, and wireless, following Jeremy’s IT Lab CCNA Mega Lab design.

VLANs and EtherChannels
Built Layer‑2 EtherChannels between distribution pairs in both offices: PAgP (Cisco‑proprietary) in Office A and LACP (open standard) in Office B, with both sides actively negotiating.
Configured all access–distribution links, including EtherChannels, as 802.1Q trunks with DTP disabled, native VLAN 1000, and specific VLANs allowed per office:

Office A: 10, 20, 40, 99

Office B: 10, 20, 30, 99

Set up VTPv2 with JeremysITLab as the domain, using distribution switches as servers and access switches as clients, then created and propagated VLANs for PCs, phones, Wi‑Fi, and management.

Access Layer and WLC Connectivity
Assigned access ports:

PCs → VLAN 10

IP phones → VLAN 20

SRV1 → VLAN 30

Configured these as access mode with DTP disabled.
Linked ASW‑A1 to WLC1 via a trunk supporting VLAN 40 (Wi‑Fi) and VLAN 99 (Management), with management untagged and DTP off.
Administratively shut down all unused access and distribution switch ports for improved security and network hygiene.

IPv4 Addressing and Layer‑3 Core
Assigned IPv4 addresses to R1 (two Internet DHCP client links, WAN point‑to‑point links, and Loopback0).
Enabled IPv4 routing on all core and distribution switches and built a Layer‑3 EtherChannel (PortChannel1) between CSW1 and CSW2 using a Cisco‑proprietary protocol with /30 addressing.
Configured all /30 and /32 addresses on CSW1, CSW2, and the distribution switches, disabling all unused Layer‑3 interfaces.
Manually configured SRV1 (10.5.0.4/24, gw 10.5.0.1) and management SVIs on all access switches using VLAN 99 and correct gateways.

HSRP Redundancy
Implemented multiple HSRPv2 groups per office matching each subnet:

Office A: DSW‑A1 active for Management & PCs, DSW‑A2 active for Phones & Wi‑Fi

Office B: DSW‑B1 active for Management & PCs, DSW‑B2 active for Phones & Servers

Used increased priority and preemption for proper failover.

Spanning Tree (Rapid PVST+)
Enabled Rapid PVST+ across all access and distribution switches so each VLAN maintains its own STP instance.
Aligned STP root bridges with HSRP actives (lowest priority), with standby devices one increment higher.
Enabled PortFast and BPDU Guard on all host interfaces (including WLC1) for faster convergence and STP protection.

OSPF and Default Routes
Configured OSPF Process 1 in Area 0 on R1’s LAN‑facing and core/distribution Layer‑3 interfaces.
Set router IDs to loopbacks, enabled OSPF on loopbacks (passive), and made SVIs passive except management.
Configured physical OSPF links (not the L3 PortChannel) as non‑broadcast to avoid DR/BDR elections.
Added two recursive default routes on R1 toward the Internet, making the G0/1/0 route a floating static (higher AD), and redistributed the default into OSPF so R1 operated as the ASBR.

DHCP, DNS, NTP, SNMP, Syslog, FTP, SSH, NAT
DHCP: Built pools on R1 for all management, PC, phone, and Wi‑Fi subnets. Excluded first 10 addresses and used SRV1 as DNS/DHCP Option provider. Distribution switches served as DHCP relays.

DNS: On SRV1, created A and CNAME records for google.com, youtube.com, jeremysitlab.com, and www.jeremysitlab.com → jeremysitlab.com.

NTP: R1 as authenticated stratum‑5 server syncing to 216.239.35.0; all network devices used R1’s loopback as NTP source with key 1 and password ccna.

SNMP: Configured with read‑only community string SNMPSTRING.

Syslog: Forwarded all severity levels to SRV1 and local buffer (8192 bytes).

FTP: R1 downloaded and booted a new IOS from SRV1 using default credentials (cisco/cisco).

SSH: Enabled SSHv2, generated large RSA keys, restricted VTY access to SSH only, created local users, used ACLs to allow only Office A PCs, and enabled synchronous logging.

NAT:

Static NAT for SRV1 → 203.0.113.113

Dynamic PAT for PC/Phone/Wi‑Fi subnets using pool POOL1 (203.0.113.200–203.0.113.207/29).

Verified Internet access and failover by toggling G0/0/0 and OSPF defaults.

LLDP and CDP
Disabled CDP, enabled LLDP on all devices for vendor‑neutral discovery.
Disabled LLDP transmit on access port F0/1 to conceal hosts.

ACLs and Layer‑2 Security
Created extended ACL OfficeA_to_OfficeB to:

Permit ICMP between Office A and Office B PC subnets

Block all other traffic between those two

Permit all else

Applied per extended ACL best practice.
Secured access ports with:

Port Security: F0/1 limited to one MAC (SRV1), sticky learning, restrict mode, notification on violations

DHCP Snooping: Active for all VLANs, uplinks trusted only, rate‑limits (15 pps untrusted, 100 pps on WLC link), Option 82 disabled

Dynamic ARP Inspection: Enabled for all VLANs, ports correctly trusted, full validation enabled.

IPv6 Preparation
Enabled IPv6 routing on R1, CSW1, and CSW2.
Assigned:

2001:db8:a::2/64 and 2001:db8:b::2/64 on R1’s Internet links

2001:db8:a1::/64 and 2001:db8:a2::/64 (using EUI‑64) on R1–CSW links
Enabled IPv6 on CSW1/CSW2 PortChannel1 (without explicit addressing).
Added two default IPv6 routes:

Recursive → 2001:db8:a::1

Floating backup → 2001:db8:b::1 (higher AD)

Wireless Configuration
Accessed WLC1 GUI (10.0.0.7, admin/adminPW12).
Created dynamic interface Wi‑Fi for VLAN 40 (IP *.4 in 10.6.0.0/24, gateway .1, DHCP 10.0.0.76, Port 1).
Configured and enabled WLAN ID 1:
Profile name/SSID → Wi‑Fi, security → WPA2 AES PSK cisco123.
Verified that both LWAPs joined WLC1 successfully (noting Packet Tracer’s DHCP limitation for wireless clients).

