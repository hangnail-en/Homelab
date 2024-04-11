# pfSense-to-FortiGate Migration
## Overall Goal
Transition all rules and filters present on pfSense to a FortiGate 40F. 

_With not allowing for OpenVPN or WireGuard connections, it won't be possible to implement the always-on VPN uplink. This has led me to opt to leave pfSense to act as the networks VPN client and will sit on a separate WAN2 interface on the FortiGate allowing traffic to be split between 2 interfaces. All other wired devices will operate under the same subnet (192.168.2.0/24) which should make this transition minimally disruptive._

---
#### Current pfSense Rules to Mirror on the FortiGate
**WAN->LAN Rules:**
![image](https://github.com/rob-vault/homelab/assets/14255092/6028a487-34db-41fb-bb08-a7e8c8d3d840)
**LAN->WAN Rules:**
![image](https://github.com/rob-vault/homelab/assets/14255092/6c190f24-6274-4edd-9c5a-ca399c41c02f)
**Port Forwards:**
![image](https://github.com/rob-vault/homelab/assets/14255092/db088d72-89fc-4748-9225-87ff59c23e1e)
Gateways:
![image](https://github.com/rob-vault/homelab/assets/14255092/a22495b8-cf1e-4609-ad3d-bb3f8262c470)
**Static Routes:**
![image](https://github.com/rob-vault/homelab/assets/14255092/320b0753-5cce-4b43-af40-068341ca19d3)

---
#### Preparing FortiGate
- The current modem model does not support a full bridge mode but effectively handles it the same through its "**DMZplus**" mode
![image](https://github.com/rob-vault/homelab/assets/14255092/b2876e17-9049-4a82-b7ef-0c4a991741e6)
- Confirmed the address was being passed down as intended on the WAN link![image](https://github.com/rob-vault/homelab/assets/14255092/32fb1558-c5fd-46c9-a4fc-c30c4cc38990)
- Configured FortiGate LAN interface to use the same IP range as pfSense![image](https://github.com/rob-vault/homelab/assets/14255092/22666da6-e9e7-4c85-bca8-14d5ff70c233)
	- Broadcasting the same DHCP range![image](https://github.com/rob-vault/homelab/assets/14255092/be142177-a7f3-4560-961b-f1cffc109c16)
	- Enabled Captive Portal![image](https://github.com/rob-vault/homelab/assets/14255092/09384aa3-5ab2-4cec-b2f2-afad7542cf8d)
- Adjusted previously configured static route to use new gateway IP![image](https://github.com/rob-vault/homelab/assets/14255092/1e80fbf6-28c9-4736-bc8a-573b8252d663)
- Ensure use of DNS over TLS![image](https://github.com/rob-vault/homelab/assets/14255092/edff0c9a-d96e-4d6f-b07f-dcd1585f36aa)
- Mirrored NextcloudPi WAN-to-NextcloudPi rule![image](https://github.com/rob-vault/homelab/assets/14255092/c92cfed9-a19d-4a6e-89d1-4908cb990e4c)
	- Configured SNAT Virtual IP (port 2000 -> 443)![image](https://github.com/rob-vault/homelab/assets/14255092/c4657b46-6734-4804-b896-163fb53f8555)
![image](https://github.com/rob-vault/homelab/assets/14255092/08906519-5fd3-425f-baa4-55490d09aa8b)
	- Confirmed successful packet routing
		- Packet capture![image](https://github.com/rob-vault/homelab/assets/14255092/97d39a02-8301-4abb-a283-8b23bebe41e3)
		- Successful Nextcloud sync![image](https://github.com/rob-vault/homelab/assets/14255092/e25db428-5db2-42ca-bca2-e9191d27111d)

***Local network migration is complete, everything is being routed properly and we can now proceed to security hardening***

---
