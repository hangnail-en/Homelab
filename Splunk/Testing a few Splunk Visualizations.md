# Testing a few Splunk Visualizations

Isolated logs for an internal Nextcloud server hosted @ 192.168.2.200
Will be working on adding in GeoIP blocks only allowing U.S. and Swiss IP's and blocking all other IP's due to the fact that most scans will come from Russia, China, Germany, etc. Most of my own traffic is routed through and will come from Swiss IP's. With all of this in mind, the visualization showing all IP's passed consistently is expected.
![Pasted image 20230919042740](https://github.com/hangnail-en/homelab/assets/14255092/b07adf78-e68c-433b-87cf-e838a20465f4)

Also visualized passed and dropped packets attempting to reach the firewall itself from the WAN interface. This is more expected due to the fact that only devices on LAN are able to reach the admin panel.
![Pasted image 20230919043214](https://github.com/hangnail-en/homelab/assets/14255092/81448d20-1e4c-4ab0-b839-001feb67afce)
