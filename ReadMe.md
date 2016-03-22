# Docker Development Server Environments
This project is used to build a development server environments based on docker.

## Installation dependencies
1. ubuntu wily 15.10 (64 bits) or Mac OS X EI Capitan



##Installation
###[docker](doc/install/docker.md)
###[docker compose](doc/install/dockercompose.md)
###[gitlab](doc/install/gitlab.md)
###[jenkins](doc/install/jenkins.md)
###[nginx](doc/install/nginx.md)

## Issues
1. [docker-compose version 2](https://github.com/sameersbn/docker-gitlab/issues/630)    
v2 removes part of the environment variables created when linking containers, so gitlab needs to add environment variables like

     	  - DB_HOST=postgresql
		    - DB_ADAPTER=postgresql
	      - DB_POST=5432
	      - DB_USER=gitlab
	      - DB_PASS=password
	      - REDIS_HOST=redisio
	      - REDIS_PORT=6379

2. Nexus: java.io.FileNotFoundException: /sonatype-work/nexus.lock (Permission denied)  
   Change the owner of directory of volume nexus on host to 200

			chown -R 200 nexus

3. Mac OS: [Permission denied when starting GitLab using docker-compose](https://github.com/sameersbn/docker-gitlab/issues/410)

   Can't use /Users as volume folder. Create a folder like /srv on docker-machine directly as volume mount folder

## Docker network conflict  
	 Docker uses the default 172.17.0.0/16 subnet for container networking. This conflicts with current company network environment.  

	 Solutions:  
	 docker-compose creates a default network like br-a9e366316a13 instead of docker0 for all the inside services.  
	 My solution is to let docker-compose use docker0 which has a customized subset 198.168.9.1/24

	 1. Check the existing docker network

	 		$ ifconfig

	 		$ docker network ls
	 		NETWORK ID          NAME                DRIVER
	 		94518e8bcbe7        bridge              bridge
	 		a9e366316a13        bridge_default      bridge
	 		7f8f068bd848        none                null
	 		7b44546b998c        host                host

	 	bridge_default is created by docker-compose, and bridge is created for docker0
	 	birdge definition can be found [here](https://help.ubuntu.com/community/NetworkConnectionBridge)

	 2. Create a customized subnet of bridge0

	 		$ sudo nano /etc/default/docker
	 			DOCKER_OPTS="--bip=198.168.9.1/24"

	 		$ sudo nano docker.service
	 			.......
	 			[Service]
	 			Type=notify
	 			EnvironmentFile=-/etc/default/docker
	 			ExecStart=/usr/bin/docker daemon $DOCKER_OPTS  -H fd://
	 			........

	 		$ sudo systemctl daemon-reload
	 		$ sudo service docker stop
	 		$ sudo service docker start
	 		$ sudo systemctl status docker		

	 		# Verify that the MASQUERADE rule for your new subnet has been added to the POSTROUTING chain:
	 		$ sudo iptables -t nat -L -n

	 3. Delete bridge_default

	 		$ sudo apt-get install bridge-utils
	 		$ sudo ip link set  br-a9e366316a13 down
	 		$ sudo brctl delbr  br-a9e366316a13
	 		$ docker network rm docker_default

	 4. Modify docker-compose.yml to use docker0

	 		$ nano docker-compose.yml
	 			network_mode: "bridge"
	 		$docker-compose up

	 5. Verify that the MASQUERADE rule for your new subnet has been added to the POSTROUTING chain:

	 		$ sudo iptables -t nat -L -n
