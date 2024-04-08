#### Initial LAMP Stack Setup for EC2 Linux AMI 2023[^1]
```
// Basic updates
sudo dnf update -y

// Install Apache and php packages
sudo dnf install -y httpd wget php-fpm php-mysqli php-json php php-devel

// Install MariaDB
sudo dnf install mariadb105-server

// Start Apache
sudo systemctl start httpd

// Enable start at boot
sudo systemctl enable httpd

// Verify running status
sudo systemctl is-enabled httpd
```
- Inbound security rules previously configured when spooling up the EC2 instance![image](https://github.com/rob-vault/homelab/assets/14255092/8108bd39-76d1-44c7-aa19-09dc909e7e9a)
- Verified successful Apache setup![image](https://github.com/rob-vault/homelab/assets/14255092/36fa800e-e87d-4edd-a654-06ce7855c29b)
- Added A record containing current public IP to domain record![image](https://github.com/rob-vault/homelab/assets/14255092/c52d547d-3b0d-4dfc-b894-73d8ea78dcfa)![image](https://github.com/rob-vault/homelab/assets/14255092/ccb2d182-c79e-481f-8427-79ecb164b6d9)


```
// Assign write permissions to user for the /var/www directory
// Add user to Apache group
sudo usermod -a -G apache ec2-user

// Sign out then back in to pick up new group
exit

// Logged back in verified group membership
groups
```
![image](https://github.com/rob-vault/homelab/assets/14255092/53f9790e-e34b-4fc3-9d1e-b3f0b3290680)


```
// Change group membership of /var/www to apache group
sudo chown -R ec2-user:apache /var/www

// Add group write permissions and set the group ID on future subdirectories, change directory permissions of /var/www and its subdirectories
sudo chmod 2775 /var/www && find /var/www -type d -exec sudo chmod 2775 {} \;

//Add group write permissions, recursively change the file permissions of /var/www and its subdirectories
find /var/www -type f -exec sudo chmod 0664 {} \;
```


---
#### Configuring SSL/TLS[^2]
```
// Install OpenSSL packages
sudo dnf install openssl mod_ssl -y

// Generate localhost.crt
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/pki/tls/private/apache-selfsigned.key -out /etc/pki/tls/certs/apache-selfsigned.crt

// Restart Apache
sudo systemctl restart httpd

// Create 4096-bit RSA private key
sudo openssl genrsa -out custom.key 4096
```
![image](https://github.com/rob-vault/homelab/assets/14255092/4a8d8f07-e080-41b3-b591-82edb1276e00)

```
// Make sure priv key ownership is restricted to root
sudo chown root:root cybersos-priv.key
sudo chmod 600 cybersos-priv.key
ls -al cybersos-priv.key
```
![image](https://github.com/rob-vault/homelab/assets/14255092/d5d5dc28-ebcd-4741-97a6-d09891021ae2)

```
// Create a CSR
sudo openssl req -new -key cybersos-priv.key -out csr.pem
```


---
#### Install CertBot[^3]
```
// Install dependencies
sudo dnf install python3 augeas-libs

// Set up Python virtual environment
sudo python3 -m venv /opt/certbot/
sudo /opt/certbot/bin/pip install --upgrade pip

// Install Certbot
sudo /opt/certbot/bin/pip install certbot certbot-apache
sudo ln -s /opt/certbot/bin/certbot /usr/bin/certbot
```
- Create virtual host on port 80 in order to run Certbot (redirects to port 443)
	- ***Not creating a vhost for port 80 will result in an error:** Unable to find a virtual host listening on port 80 which is currently needed for Certbot to prove to the CA that you control your domain. Please add a virtual host for port 80.*[^4]


```
<VirtualHost *:80>
        ServerName cybersos.tech
        Redirect / https://cybersos.tech/
</VirtualHost>
```

```
// Run Certbot setup
sudo certbot --apache
```

Results:
```
Which names would you like to activate HTTPS for?
We recommend selecting either all domains, or all domains in a VirtualHost/server block.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
1: cybersos.tech
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Select the appropriate numbers separated by commas and/or spaces, or leave input
blank to select all options shown (Enter 'c' to cancel): 1
Requesting a certificate for cybersos.tech

Successfully received certificate.
Certificate is saved at: /etc/letsencrypt/live/cybersos.tech/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/cybersos.tech/privkey.pem
This certificate expires on 2024-07-07.
These files will be updated when the certificate renews.

Deploying certificate
Successfully deployed certificate for cybersos.tech to /etc/httpd/conf.d/cybersos.tech-le-ssl.conf
Congratulations! You have successfully enabled HTTPS on https://cybersos.tech

NEXT STEPS:
- The certificate will need to be renewed before it expires. Certbot can automatically renew the certificate in the background, but you may need to take steps to enable that functionality. See https://certbot.org/renewal-setup for instructions.
```

```
// Set up automatic renewal
echo "0 0,12 * * * root /opt/certbot/bin/python -c 'import random; import time; time.sleep(random.random() * 3600)' && sudo certbot renew -q" | sudo tee -a /etc/crontab > /dev/null
```
- Initially received "TOO_MANY_REDIRECTS" error in browser indicating that there is a misconfiguration in the .conf files causing a redirect loop

![image](https://github.com/rob-vault/homelab/assets/14255092/0c25fb21-9d67-4b13-9197-43bb34ef2789)

- Backed up conf file created to allow vhost port 80 for Certbot to run
	- ```sudo mv cybersos.tech.conf cybersos.tech.conf.bak```
- Copied contents into conf file created by LetsEncrypt and changed **ServerName** of vhost for port 443 to *https://cybersos.tech* and removed redirect which was causing the loops
	```
	<IfModule mod_ssl.c>
	<VirtualHost *:80>
	        ServerName cybersos.tech
	        Redirect / https://cybersos.tech/
	RewriteEngine on
	RewriteCond %{SERVER_NAME} =cybersos.tech
	RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,NE,R=permanent]
	</VirtualHost>
	
	
	
	<VirtualHost *:443>
	        ServerName https://cybersos.tech
	
	SSLCertificateFile /etc/letsencrypt/live/cybersos.tech/fullchain.pem
	SSLCertificateKeyFile /etc/letsencrypt/live/cybersos.tech/privkey.pem
	Include /etc/letsencrypt/options-ssl-apache.conf
	</VirtualHost>
	```
- Restarted apache and confirmed successful redirects without certificate errors
![image](https://github.com/rob-vault/homelab/assets/14255092/fdf8ad69-1a90-44a7-be88-5456096f0f91)
![image](https://github.com/rob-vault/homelab/assets/14255092/f7058634-4df2-4f42-9341-85621f1d08c4)


---
# Footnotes

[^1]: https://docs.aws.amazon.com/linux/al2023/ug/ec2-lamp-amazon-linux-2023.html
[^2]: https://docs.aws.amazon.com/linux/al2023/ug/SSL-on-amazon-linux-2023.html
[^3]: https://certbot.eff.org/instructions?ws=apache&os=pip
[^4]: https://community.letsencrypt.org/t/unable-to-find-virtual-host-listening-on-port-80/202598
