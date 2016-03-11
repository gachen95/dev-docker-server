# [Install docker](https://docs.docker.com/engine/installation/linux/ubuntulinux/)
1. Install docker
	
		$ sudo apt-get update
		$ sudo apt-get install apt-transport-https ca-certificates
		$ sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
		$ sudo nano /etc/apt/sources.list.d/docker.list	
			deb https://apt.dockerproject.org/repo ubuntu-wily main	
		$ sudo apt-get update
		$ sudo apt-get purge lxc-docker
		$ sudo apt-cache policy docker-engine
		$ sudo apt-get install linux-image-extra-$(uname -r)	
		$ sudo apt-get install docker-engine
		$ sudo service docker start

2. Add the existing user ci to Docker group to avoid use sudo in front of docker
	
		$ sudo usermod -aG docker ci
		
		logout/relogin

3. Configure Docker to start on boot
	
		$ sudo systemctl enable docker

4. Upgrade

		$ sudo apt-get upgrade docker-engine

5. Check docker status
	
		$ sudo systemctl status docker.service
		
		$ docker version
		Client:
		Version:      1.10.2
		API version:  1.22
		Go version:   go1.5.3
		Git commit:   c3959b1
		Built:        Mon Feb 22 21:40:35 2016
		OS/Arch:      linux/amd64
		
		Server:
		Version:      1.10.2
		API version:  1.22
		Go version:   go1.5.3
		Git commit:   c3959b1
		Built:        Mon Feb 22 21:40:35 2016
		OS/Arch:      linux/amd64

6. Find app location  
		$ whereis docker  
		$ which docker

7. docker store location  

		/var/lib/docker/
8. Common docker command    
	1. Delete none images and recycle spaces
	
			$ docker images --no-trunc| grep none | awk '{print $3}' | xargs -r docker rmi  
	2. Delete Existed container
	
			$ docker rm `docker ps -a | grep Exited | awk '{print $1 }’`
	3. Delete never used images
	
			$ docker rmi `docker images -aq`  
			$ docker stop $(docker ps -a -q)
			$ docker rm $(docker ps -a -q)
