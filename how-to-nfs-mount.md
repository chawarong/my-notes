How to create an NFS mount point on CentOS 6.4
==============================================

This is the instruction to create a mount point on CentOS 6.4

[Reference](http://www.techotopia.com/index.php/Using_NFS_to_Share_CentOS_6_Folders_with_Remote_Linux_and_UNIX_Systems)  
[NFSv4Howto](https://help.ubuntu.com/community/NFSv4Howto)  

### On a server

1. Check if nfs is installed

		$ rpm -qa | grep nfs  
		$ service nfs status`

2. Add a mount point 

		$ vi /etc/exports

  In the above line, <export> is replaced by the directory to be exported, <host1> is the name or 
  IP address of the system to which access is being granted and <options> represents the restrictions 
  that are to be imposed on that access (read only, read write etc). 
  
  > format: &lt;export&gt; &lt;host1&gt;(&lt;options&gt;) &lt;host2&gt;(&lt;options&gt;)...

  > example:  
  > /datafiles 192.168.2.38(ro)  
  > /home/demo *(rw)  
  > /tmp       *(rw,sync)  
  > /vol1      192.168.2.21(ro,sync)  


3. The directories must be exported.

		$ exportfs -a

	To view exported file system

		$ exportfs

4. Turn on nfs at start up  

		$ chkconfig nfs on
		$ chkconfig --list nfs
		$ service nfs start

Note: for each options available, read [this link](http://www.centos.org/docs/5/html/Deployment_Guide-en-US/s1-nfs-server-config-exports.html)

### On a client

1. Make a local folder

		$ mkdir /myfolder

2. Issue this command 

		$ sudo mount -t nfs -v [ip or hostname]:/myfolder /myfolder


Troubleshooting
---------------

* Most of the time, keep an eye on /var/log/message. This is a typical error when nfs rejects a client connect

		rpc.mountd[30666]: refused mount request from x.x.x.x for /ster_storage (/ster_storage): illegal port 64877

	it's because it can't resolve name of the client connected to it. NFS typically wants name resolution for 
	clients connecting to it, and if it can't get that, then it will deny access.

	An entry in the hosts file is usually sufficient for me.

* Check with firewall and SELinux

		$ iptables -L  
		$ getenforce  

* On Mac client, it connects to NFS using insecure port. Using the insecure option allows clients such as Mac OS X to connect on insecure ports.
  [link](http://blogaristoo.lqx.net/index.php/mounting-a-linux-nfs-server-on-mac-os-x-10-6)

		rpc.mountd[31257]: authenticated mount request from x.x.x.x:1017 for /foster_storage (/foster_storage)


		$ vi /etc/exports
		/datafiles 192.168.0.0/16(ro,insecure)
