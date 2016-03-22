# Docker Development Server Environments
This project is used to build a development server environments based on docker.

## Installation dependencies
1. ubuntu wily 15.10 (64 bits) or Mac OS X EI Capitan



##Installation
###[docker](doc/install/docker.md)
###[docker compose](doc/install/dockercompose.md)
###[gitlab](doc/install/gitlab.md)
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
