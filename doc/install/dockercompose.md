
# Install Docker Compose

		$ curl -L https://github.com/docker/compose/releases/download/1.6.0/docker-compose-`uname -s`-`uname -m` > docker-compose
		$ sudo mv docker-compose /usr/local/bin/docker-compose
		$ sudo chmod +x /usr/local/bin/docker-compose
		$ docker-compose --version
			docker-compose version 1.6.0, build d99cad6

## Docker Compose Commands		
	
		$ docker-compose up 
		$ docker-compose up -d   // detached mode
		$ docker-compose ps      // see what is currently running
		$ docker-compose stop
		$ docker-compose kill
		$ docker-compose rm
		
		$ docker-compose run <name> bash  // debug
 
