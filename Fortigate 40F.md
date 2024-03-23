# Fortigate 40F Setup

https://172.16.10.1/

admin
[password]

---
Arrived with a configuration still applied to it
Factory reset the unit
- Reboot the unit
- Waited for Status light to begin flashing to indicate FortiOS began booting
- Pressed Reset button within the 30 sec window
Logged in https://192.168.1.99/
Changed default pass

admin
[password]

Went through startup wizard
- Hostname: underworld-fort
- Updated Firmware following upgrade path
	Current version
	FortiOS v6.4.7 build8726 (GA) -> FortiOS v6.4.14 build2093 (GA) -> FortiOS v7.0.12 build0523 -> FortiOS v7.2.6 build1575 -> FortiOS v7.4.1 build2463

Changed LAN IP, DHCP Range and enabled Captive Portal
![Pasted image 20231018012035](https://github.com/hangnail-en/homelab/assets/14255092/650c1584-ca73-4e19-8c1b-c4910a5fc967)
![Pasted image 20231018012051](https://github.com/hangnail-en/homelab/assets/14255092/abbf3005-efd8-411a-add6-a310fe3e3bca)
![Pasted image 20231018012102](https://github.com/hangnail-en/homelab/assets/14255092/987ca0e3-9fd4-423c-82ea-6ab9013e27ab)
![Pasted image 20231018011607](https://github.com/hangnail-en/homelab/assets/14255092/c2b1dcd9-d18e-48bf-b5d0-33ae01132c51)


Enabled syslog -> Splunk server

![Pasted image 20231018021340](https://github.com/hangnail-en/homelab/assets/14255092/ec6ae9a3-bd7f-4407-ba88-bf866a143b18)
