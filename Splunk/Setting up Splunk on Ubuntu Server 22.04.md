# Setting up Splunk on Ubuntu Server 22.04

URL: http://192.168.2.195:8000/
Admin Creds:

	User: spelunker
 	Pass: [password]

---
Installed Ubuntu 22.04 in a VM
![Pasted image 20230919012809](https://github.com/hangnail-en/homelab/assets/14255092/355da08e-41e4-4273-837c-9fe47cd901f7)

Brought all packages up to date
![Pasted image 20230919014123](https://github.com/hangnail-en/homelab/assets/14255092/b42610d9-930c-4a7f-9a3c-7d37dae887f0)

```
sudo apt update && sudo apt upgrade
```

Downloaded Splunk package
```
sudo wget -O splunk-9.1.1-64e843ea36b1-linux-2.6-amd64.deb "https://download.splunk.com/products/splunk/releases/9.1.1/linux/splunk-9.1.1-64e843ea36b1-linux-2.6-amd64.deb"

sudo dpkg -i splunk-9.1.1-64e843ea36b1-linux-2.6-amd64.deb
```
![Pasted image 20230919015148](https://github.com/hangnail-en/homelab/assets/14255092/d814680f-20dc-41eb-9f5d-1db34d441a77)

Used the official Splunk docs to complete installation:
https://docs.splunk.com/Documentation/Splunk/9.1.1/Installation/StartSplunkforthefirsttime
```
cd /opt/splunk/bin
sudo ./splunk start
```

Configured to start automatically on bootup:
https://docs.splunk.com/Documentation/Splunk/9.1.1/Admin/ConfigureSplunktostartatboottime
```
sudo /opt/splunk/bin/splunk enable boot-start
```

Rebooted and verified service is configured properly for boot-start
![Pasted image 20230919021035](https://github.com/hangnail-en/homelab/assets/14255092/8106f44a-2275-4fe4-be65-e713aec0acae)

Adjusted profile preference to use a dark theme (obligatory), EST time and to default to the Search & Reporting app
![Pasted image 20230919021355](https://github.com/hangnail-en/homelab/assets/14255092/2f78f690-7244-4ac8-b924-4b2dba6cf933)

Added Allow port 7001 rule to pfSense
![Pasted image 20230919023530](https://github.com/hangnail-en/homelab/assets/14255092/ca598074-b399-418b-857c-6da49318968c)

Created new index and input to pull pfSense data into the fw index
![Pasted image 20230919024111](https://github.com/hangnail-en/homelab/assets/14255092/b308d33b-4e41-4fae-9e03-5155956a5fa7)
![Pasted image 20230919024357](https://github.com/hangnail-en/homelab/assets/14255092/653db7df-4132-41ec-960e-3ad7678ab60d)

Enabled remote logging in pfSense
![Pasted image 20230919024636](https://github.com/hangnail-en/homelab/assets/14255092/41aee27d-c640-48cc-a4ad-d1d48f0baf85)

Attempted to browse data in the "fw' index and encountered an error regarding minimum free disk space
```
Search not executed: The minimum free disk space (5000MB) reached for /opt/splunk/var/run/splunk/dispatch. user=spelunker., concurrency_category="historical", concurrency_context="user_instance-wide", current_concurrency=0, concurrency_limit=5000
```
Adjusted quota to 2000MB

Adjusted ufw on the server to allow OpenSSH and ports 7001
After following over a GUI version guide, data began populating the fw index:
https://hurricanelabs.com/splunk-tutorials/your-all-in-one-guide-to-setting-up-pfsense-and-suricata-in-splunk/
![Pasted image 20230919033931](https://github.com/hangnail-en/homelab/assets/14255092/43b0df57-1dbc-4103-96c1-3adb6759b11c)
