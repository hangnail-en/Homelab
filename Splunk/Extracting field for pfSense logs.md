# Extracting field for pfSense logs

Extracted a few fields (looks like the pfSense app imported earlier doesn't provide any fields???)
![Pasted image 20230919035450](https://github.com/hangnail-en/homelab/assets/14255092/c065a347-54b1-4668-a6a9-b4726f1cd467)

Extracted the following parameters:
```
	- date
		- Date of log
	- time
		- Time log occurred
	- fw_hostname
		- Hostname of the firewall (useful if there are multiple firewalls)
	- rule_num
		- Number associated with the rule matched on the fw
	- interface
		- Shows which interface the info was logged for, useful if there are multiple gateways for example
	action
		- Pass or Blocked
	- protocol
		- TCP, UDP, etc.
	- src_ip
		- Source IP
	- dest_ip
		- Destination IP
	- src_prt
		- Source Port
	- dest_prt
		- Destination Port
```

![Pasted image 20230919040404](https://github.com/hangnail-en/homelab/assets/14255092/1b95247e-1b0e-43b0-8f7a-9e35b9681b22)
![Pasted image 20230919040607](https://github.com/hangnail-en/homelab/assets/14255092/f3c1f37e-c4ad-420b-813f-1b81c57ac81a)

Fields are being extracted properly and are now fairly easily searchable
![Pasted image 20230919041150](https://github.com/hangnail-en/homelab/assets/14255092/0100ce5a-e278-4711-84d4-13f6f82ca0e4)
