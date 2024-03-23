# Integrating Splunk with Fortigate 40F

Installed Fortinet add-ons for Splunk
![Pasted image 20231018022712](https://github.com/hangnail-en/homelab/assets/14255092/636fdaf9-f1ea-4ec1-9b77-3540637242d4)

Allowed traffic to port 514

![Pasted image 20231018023238](https://github.com/hangnail-en/homelab/assets/14255092/56fa965e-0321-4cbb-93d8-a4d4f6ea218a)

Added a UDP Data Input to receive logs from the Fortigate to index "fw"
![Pasted image 20231018030244](https://github.com/hangnail-en/homelab/assets/14255092/67ca9f94-d5c4-4364-883c-fddd6bbf9acd)

![Pasted image 20231018030302](https://github.com/hangnail-en/homelab/assets/14255092/41113fae-31a1-4fe9-b777-9a32bd7ef478)

Logs are successfully populating
![Pasted image 20231018032402](https://github.com/hangnail-en/homelab/assets/14255092/cb0c8941-96d7-4ea0-a693-45ab449d774a)

Need to configure field extractions
