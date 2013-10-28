how-to-migrate-jenkins-to-new-server.md

This how-to contains the instruction to migrate existing jenkins to a new server. 
I use Ubuntu 12.04. 

* download a war file from [jenkins website](http://jenkins-ci.org/)

* On the old host, stop jenkins

		$ sudo service jenkins stop
		$ sudo service apache2 stop

* On the old host, archive /var/lib/jenkins

		$ cd /var/lib
		$ tar cvzf jenkins-`date +"%Y%m%d%H%M%S"`.tgz ./jenkins

* On the new host, install Oracle Java

		$ sudo apt-add-repository ppa:webupd8team/java
		$ sudo apt-get update
		$ sudo apt-get install oracle-java7-installer        ;# accept license agreement, too.

* On the new host, install jenkins 

  		$ wget -q -O - http://pkg.jenkins-ci.org/debian/jenkins-ci.org.key | sudo apt-key add -
  		$ sudo sh -c 'echo deb http://pkg.jenkins-ci.org/debian binary/ > /etc/apt/sources.list.d/jenkins.list'
  		$ sudo apt-get update 
  		$ sudo apt-get install jenkins
  		Reading package lists... Done
		Building dependency tree       
		Reading state information... Done
		The following extra packages will be installed:
		  daemon
		The following NEW packages will be installed:
		  daemon jenkins
		0 upgraded, 2 newly installed, 0 to remove and 130 not upgraded.
		Need to get 61.0 MB of archives.
		After this operation, 67.1 MB of additional disk space will be used.
		Do you want to continue [Y/n]? y
		Get:1 http://th.archive.ubuntu.com/ubuntu/ precise/universe daemon amd64 0.6.4-1 [98.2 kB]
		Get:2 http://pkg.jenkins-ci.org/debian/ binary/ jenkins 1.537 [60.9 MB]
		Fetched 61.0 MB in 4min 32s (224 kB/s)                                                                                           
		Selecting previously unselected package daemon.
		(Reading database ... 67487 files and directories currently installed.)
		Unpacking daemon (from .../daemon_0.6.4-1_amd64.deb) ...
		Selecting previously unselected package jenkins.
		Unpacking jenkins (from .../archives/jenkins_1.537_all.deb) ...
		Processing triggers for man-db ...
		Processing triggers for ureadahead ...
		Setting up daemon (0.6.4-1) ...
		Setting up jenkins (1.537) ...
		Adding system user `jenkins' (UID 108) ...
		Adding new user `jenkins' (UID 108) with group `nogroup' ...
		Not creating home directory `/var/lib/jenkins'.
		 * Starting Jenkins Continuous Integration Server jenkins                                                                  [ OK ] 


  		# verify
		$ cd /var/lib/jenkins/
		$ ls -l
		total 32
		-rw-r--r--  1 jenkins nogroup  159 Oct 28 16:16 hudson.model.UpdateCenter.xml
		-rw-------  1 jenkins nogroup 1675 Oct 28 16:16 identity.key
		drwxr-xr-x  2 jenkins nogroup 4096 Oct 28 16:16 jobs
		-rw-r--r--  1 jenkins nogroup  907 Oct 28 16:16 nodeMonitors.xml
		drwxr-xr-x 16 jenkins nogroup 4096 Oct 28 16:16 plugins
		-rw-r--r--  1 jenkins nogroup   64 Oct 28 16:16 secret.key
		-rw-r--r--  1 jenkins nogroup    0 Oct 28 16:16 secret.key.not-so-secret
		drwxr-xr-x  2 jenkins nogroup 4096 Oct 28 16:21 updates
		drwxr-xr-x  2 jenkins nogroup 4096 Oct 28 16:16 userContent

Open a browser and browse to http://[hostname]:8080 to verify.


 What does this package do?

	   - Jenkins will be launched as a daemon up on start. See /etc/init.d/jenkins for more details.  
	   - The 'jenkins' user is created to run this service.  
	   - Log file will be placed in /var/log/jenkins/jenkins.log. Check this file if you are troubleshooting Jenkins.  
	   - /etc/default/jenkins will capture configuration parameters for the launch. (_don't change anything here_)  
	   - By default, Jenkins listen on port 8080. Access this port with your browser to start configuration.  

* On the new host, extract the archive of /var/lib/jenkins from the old host
	
	    $ cd /var/lib	
	    $ sudo mv jenkins jenkins-original
		$ scp [user]@[old host]:/var/lib/jenkins-20131028150000.tgz ~/.
		$ sudo tar xvzf ~/jenkins-20131028150000.tgz
		$ sudo service jenkins restart 
	
* configure nginx

#### Configure system

* go to Manage Jenkins > Configure system, then find "Jenkins URL" and fill in the url.
* do configure "SSH Server", too.
