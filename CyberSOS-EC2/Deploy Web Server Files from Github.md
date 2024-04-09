#### Generating SSH Key[^1]
```bash
// Generate key & configure passphrase
ssh-keygen -t ed25519 -C "c***********@t**********mail.com"

// Add SSH key to the ssh-agent
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

- Added public key to Github SSH auth keys![image](https://github.com/rob-vault/homelab/assets/14255092/b78b80eb-8e2f-4484-a27a-13395754184c)

---
#### Test SSH connection[^2]
```
ssh -T git@github.com
Hi rob-vault! You've successfully authenticated, but GitHub does not provide shell access.
```
![image](https://github.com/rob-vault/homelab/assets/14255092/b3c3a82d-5ab9-4ca3-8c9c-3e57fb49c098)


- Cloned private repo containing web server files and copied to ```
```shell
[ec2-user@ip-172-31-0-5 ~]$ git clone git@github.com:rob-vault/sos-temp.git
Cloning into 'sos-temp'...
remote: Enumerating objects: 222, done.
remote: Counting objects: 100% (222/222), done.
remote: Compressing objects: 100% (131/131), done.
remote: Total 222 (delta 91), reused 215 (delta 84), pack-reused 0
Receiving objects: 100% (222/222), 5.87 MiB | 33.95 MiB/s, done.
Resolving deltas: 100% (91/91), done.

[ec2-user@ip-172-31-0-5 ~]$ cd sos-temp
[ec2-user@ip-172-31-0-5 sos-temp]$ ls
__MACOSX  alya
[ec2-user@ip-172-31-0-5 sos-temp]$ cd alya
[ec2-user@ip-172-31-0-5 alya]$ ls
gulpfile.js  html  package.json  scss
[ec2-user@ip-172-31-0-5 alya]$ ls /var/www/
cgi-bin  html

[ec2-user@ip-172-31-0-5 alya]$ cp -r html/ /var/www/
```

- Confirmed the site is now displaying the html pages retrieved from Github![image](https://github.com/rob-vault/homelab/assets/14255092/4d79b374-bfb3-44ee-aa58-23d4643bb002)


---
# Footnotes

[^1]: https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent
[^2]: https://docs.github.com/en/authentication/connecting-to-github-with-ssh/testing-your-ssh-connection

- [Cloning Git Repository via SSH](https://phoenixnap.com/kb/git-clone-ssh)
