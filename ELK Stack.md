# ELK Stack

Elasticsearch and Kibana stack running in Docker on Windows 11

Installed and restarted
Added user to the docker-users group
Installed the WSL 2 Linux kernel update

Ran cmd as admin:
	docker network create elastic
	docker pull docker.elastic.co/elasticsearch/elasticsearch:8.5.0
	docker run --name elasticsearch --net elastic -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -t docker.elastic.co/elasticsearch/elasticsearch:8.5.0

Output from term:
	✅ Elasticsearch security features have been automatically configured!
	✅ Authentication is enabled and cluster connections are encrypted.
	
	ℹ️  Password for the elastic user (reset with `bin/elasticsearch-reset-password -u elastic`):
	  Z7Wc3KV*-=P0hcllEWAY
	
	ℹ️  HTTP CA certificate SHA-256 fingerprint:
	  975ae523e7499ffab81efab5541f34ffd1c7f0775abdf3432bfe12e113786f43
	
	ℹ️  Configure Kibana to use this cluster:
	• Run Kibana and click the configuration link in the terminal when Kibana starts.
	• Copy the following enrollment token and paste it into Kibana in your browser (valid for the next 30 minutes):
	  eyJ2ZXIiOiI4LjUuMCIsImFkciI6WyIxNzIuMTguMC4yOjkyMDAiXSwiZmdyIjoiOTc1YWU1MjNlNzQ5OWZmYWI4MWVmYWI1NTQxZjM0ZmZkMWM3ZjA3NzVhYmRmMzQzMmJmZTEyZTExMzc4NmY0MyIsImtleSI6Ik1BLVBUNFFCdmt4RVhpd3hUZkkyOjBLMXhWVkxEUkpLai1BbnhYU3d5M2cifQ==
	
	ℹ️ Configure other nodes to join this cluster:
	• Copy the following enrollment token and start new Elasticsearch nodes with `bin/elasticsearch --enrollment-token <token>` (valid for the next 30 minutes):
	  eyJ2ZXIiOiI4LjUuMCIsImFkciI6WyIxNzIuMTguMC4yOjkyMDAiXSwiZmdyIjoiOTc1YWU1MjNlNzQ5OWZmYWI4MWVmYWI1NTQxZjM0ZmZkMWM3ZjA3NzVhYmRmMzQzMmJmZTEyZTExMzc4NmY0MyIsImtleSI6Ik1RLVBUNFFCdmt4RVhpd3hUZkkyOjJpcDFtdFJ0VGVHandGMEE3U0ppQncifQ==
	
	  If you're running in Docker, copy the enrollment token and run:
	  `docker run -e "ENROLLMENT_TOKEN=<token>" docker.elastic.co/elasticsearch/elasticsearch:8.5.0`

Ran cmd as admin to start up Kibana:
	docker pull docker.elastic.co/kibana/kibana:8.5.0
	docker run --name kibana --net elastic -p 5601:5601 docker.elastic.co/kibana/kibana:8.5.0

Integrated Kibana with Elastic using the token generated in output earlier
Services are running properly
Added "Add Windows Integration"
Installed agent on control center host
Opted out of using Fleet to manage agents, updated agent file
Logs are being collected from the Windows system

---
Integrating Elastic with pfSense
https://docs.elastic.co/en/integrations/pfsense
Configured pfSense to send logs to Elastic via syslog
Added "pfSense" intergration in Elastic

Enabled port 844 for SSH on pfSense to use shell to configure agent

---
Needed to open up port 9001 on the docker image for use with the pfSense syslog function
Ports can't modified on existing containers
Committed the running elasticsearch container to keep all the changes that have already been made

Relaunch From Docker Commit (https://www.baeldung.com/linux/assign-port-docker-container#relaunch-from-docker-commit)
	
	Instead of launching a new container from zero, **we can commit the old Docker container to create a new Docker image** and use that to start a new container with the right ports open.
	
	Since we’re committing the old Docker container, the persistent state of the first will be available in the newly launched one.
	
	Let’s try this out.
	
	First, we’ll stop our Docker container and create its image using the [_docker commit_](https://docs.docker.com/engine/reference/commandline/commit/) command:
	
	```bash
	$ docker stop httpd-container
	httpd-container
	$ docker commit httpd-container httpd-image
	sha256:33da33fcad051c90ac9b7dea9b2dbda442767e05ddebd8d6db8ac6893ef4ef40
	```
	
	Next, we’ll remove the container and start a fresh container using the image that we just created.
	
	We need to make sure we add/update the port mappings in the _run_ command this time:
	
	```bash
	$ docker rm httpd-container
	httpd-container
	$ docker run -d -p 83:80 --name httpd-container httpd-image
	dd2535c477ad74e80b3642abca9055efacb89eaf14572b91f91bf20cd3f0cbf3
	```
	
	Now we have a new container with the right port mappings to pick up where the previous one left off.

pfSense is still not running but the ELK stack is running and properly parsing Windows logs
Alerts can't be configured because of license restrictions but the parts given in the free version are good nonetheless

![Pasted image 20230919160039](https://github.com/hangnail-en/homelab/assets/14255092/b44dc6a8-d6a1-412d-93ee-7d99999bbb3e)
