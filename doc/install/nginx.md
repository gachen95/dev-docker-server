# NGINX

Nginx is used a reverse proxy for dockered sonar, nexus, etc.

## Reverse proxy with multiple domain/subdomain names
if sonar and nexus have separate domain names, like sonar.mydomain.com, nexus.mydomain.com, it will be easier to configurate nginx.  
Under conf.d dir, create separate conf file for nexus, sonar.   

For example, conf.d/sonar_server.conf

        ## sonar
		upstream sonar {  
			server sonar:9000;  			 
		}  
		server {  
			listen 80;  
			server_name sonar.mydomain.com;  
			access_log /var/log/nginx/nginx.access.log main;  
			proxy_set_header Host $host;  
			proxy_set_header X-Real-IP $remote_addr;  
			proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
			  
			location / {
				# proxy requests onto sonar server on a different docker container  
				proxy_pass http://sonar/;  

				proxy_redirect default;  
				proxy_read_timeout 120;  
			}  
		}  

## Reverse proxy with subpaths/subdirectories
If there are no enough domain names for each of nexus, sonar,etc., they have to be treated as subpaths/subdirectories.
That means nginx uses location to serve them. Conf file is like


        ## sonar
		upstream sonar {  
			server sonar:9000;  			 
		}  
		server {  
			listen 80;  
			server_name mydomain.com;  
			access_log /var/log/nginx/nginx.access.log main;  
			proxy_set_header Host $host;  
			proxy_set_header X-Real-IP $remote_addr;  
			proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
			  
			location /sonar/ {  
				proxy_pass http://sonar/;  
				proxy_redirect default;  
				proxy_read_timeout 120;  
			}  
		}  
		
but there will be many open failed errors, like

 		 open() "/usr/share/nginx/html/js/bundles/dashboard.js" failed (2: No such file or directory)
		
### Solutions:
The solution is to change the web context path of sonar, nexus, etc.  

### sonarqube
1.  create and edit /sys/docker/conf/sonar/sonar.properties
			sonar.web.context=/sonar			
2.  Edit docker-compose.yml

		volumes:   
		- /sys/docker/conf/sonar/sonar.properties:/opt/sonarqube/conf/sonar.properties
    
3.  Change nginx's server conf  
    From   	
   		proxy_pass http://sonar/;
    To    
        proxy_pass http://sonar/sonar/;
		

### nexus
1. Edit docker-compose.yml
    environment:
      - CONTEXT_PATH=/nexus
	  
  [Notably](https://github.com/sonatype/docker-nexus):   
  /opt/sonatype/nexus/conf/nexus.properties is the properties file. Parameters (nexus-work and nexus-webapp-context-path) definied here are overridden in the JVM invocation.
  
2.   Change nginx's server conf  
    From   	
   		proxy_pass http://nexus/;
    To    
        proxy_pass http://nexus/nexus/;
					
		
## Reference
1. [Understanding Nginx HTTP Proxying, Load Balancing, Buffering, and Caching](https://www.digitalocean.com/community/tutorials/understanding-nginx-http-proxying-load-balancing-buffering-and-caching)
2. [nginx reverse ssl proxy with multiple subdomains](http://serverfault.com/questions/538803/nginx-reverse-ssl-proxy-with-multiple-subdomains)
3. [jwilder/nginx-proxy](https://github.com/jwilder/nginx-proxy)
4. [Running SonarQube behind a Proxy](http://docs.sonarqube.org/display/SONAR/Running+SonarQube+behind+a+Proxy)
5. [Running Nexus Behind a Reverse Proxy](https://books.sonatype.com/nexus-book/reference/install-sect-proxy.html)
6. [Running Jenkins from a subdomain](https://wiki.jenkins-ci.org/display/JENKINS/Jenkins+behind+an+NGinX+reverse+proxy)
7. [Running Jenkins behind Apache](https://wiki.jenkins-ci.org/display/JENKINS/Running+Jenkins+behind+Apache)
