
#Install [gitlab](http://doc.gitlab.com/omnibus/docker/README.html)

## [install-gitlab-using-docker-compose](http://doc.gitlab.com/omnibus/docker/#install-gitlab-using-docker-compose)

## [Environment Variables](http://doc.gitlab.com/ce/administration/environment_variables.html)

		// for all users
		$sudo nano  /etc/environment
		GITLAB_HOST='http://gitlab.example.com:10080'

## [GITLAB_OMNIBUS_CONFIG](https://gitlab.com/gitlab-org/omnibus-gitlab/blob/master/files/gitlab-config-template/gitlab.rb.template)

		# Email
        gitlab_rails['gitlab_email_enabled'] = true
        gitlab_rails['gitlab_email_from'] = 'Richard@mail.ca'
        gitlab_rails['gitlab_email_display_name'] = 'Gitlab'
        gitlab_rails['gitlab_email_reply_to'] = 'noreply@mail.ca'

		# Timezone
        gitlab_rails['time_zone'] = 'America/New_York'        

		# smtp
        gitlab_rails['smtp_enable'] = true        
        gitlab_rails['smtp_address'] = "smtpserver.mail.ca"
        gitlab_rails['smtp_domain'] = "mail.ca" 	  
        gitlab_rails['smtp_openssl_verify_mode'] = 'none'


##[Scheduling a backup](https://gitlab.com/gitlab-org/omnibus-gitlab/blob/629def0a7a26e7c2326566f0758d4a27857b52a3/README.md#backups)
 To schedule a cron job that backs up your repositories and GitLab metadata, use the root user:

		$ sudo su -
		$ crontab -e
			#There, add the following line to schedule the backup for everyday at 2 AM:
			0 2 * * * /opt/gitlab/bin/gitlab-rake gitlab:backup:create

## gitlab commands

		#https://gitlab.com/gitlab-org/gitlab-ce/blob/master/doc/raketasks/maintenance.md
		$ gitlab-rake gitlab:env:info
		$ gitlab-rake gitlab:check
		$ gitlab-rake cache:clear

		#check error logs
		$ gitlab-ctl tail

##Error
### OpenSSL::SSL::SSLError: hostname "smtpserver.mail.ca" does not match the server certificate  
Solutions:  
gitlab_rails['smtp_openssl_verify_mode'] = 'none'   

###url is not correct in Reset password instructions email  
http://a6ac148ac0b1/users/password/edit?reset_password_token=_YsEE-M7KpXMP5ejw6Sq    
a6ac148ac0b1 is CONTAINER ID   

Solutions:  
a.) set external_url in docker-compose.yml

	external_url "http://gitlab.example.com"

but port 10080 can't be set in external_url, like "http://gitlab.example.com:10080". Please see [issues](https://gitlab.com/gitlab-org/gitlab-ce/issues/1551)  
if use external_url "http://gitlab.example.com:10080", must change

    ports:
      - "10080:80"

to	  

	    ports:
      - "10080:10080"
	  
## Reference
1. [sameersbn/docker-gitlab](https://github.com/sameersbn/docker-gitlab#available-configuration-parameters)
