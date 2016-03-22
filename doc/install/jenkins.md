#[jenkins](https://hub.docker.com/r/library/jenkins/)

## [Plugins]( https://wiki.jenkins-ci.org/display/JENKINS/Plugins)

## Issues
1. [Permission denied - /var/jenkins_home/copy_reference_file.log](https://github.com/jenkinsci/docker/issues/177)

		touch: cannot touch ‘/var/jenkins_home/copy_reference_file.log’: Permission denied
		Can not write to /var/jenkins_home/copy_reference_file.log. Wrong volume permissions?
		
### Solution  
[Handling permissions in volumes with gosu](https://github.com/jenkinsci/docker/issues/158)		
		
2. gpg: keyserver receive failed: keyserver error
### Reason
Firewall blocks the port 11371.
### Solution
1.unblock the port in your firewall
2. Use other key server instead
   gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4
   or 
   gpg --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4
3. import key
Find and open the key from the key server.
Copy it's contents into a text file.
Go to System Tool > Preferences > Software Sources > Authentication > Add key, and select the text file created.


https://github.com/capitalone/Hygieia/blob/master/test-servers/jenkins/Dockerfile

3. [Proposal to add some 'cookbook' docker files](https://github.com/jenkinsci/docker/issues/208)	

4. curl: (3) Illegal characters found in URL when downloading plugins.txt

a. check the line breaks in your plugins.txt to use UNIX format
b. check if there are blank lines in plugins.txt


http://blog.philipphauer.de/tutorial-continuous-delivery-with-docker-jenkins/
http://jigsawye.com/2015/09/25/gitlab-ce-in-docker/	
		
		

9.docker-compose example
https://github.com/orctom/docker/tree/master/sonar
https://github.com/harbur/docker-sdlc
https://github.com/lotaris/docker-demo
https://github.com/CenterForOpenScience/docker-library
