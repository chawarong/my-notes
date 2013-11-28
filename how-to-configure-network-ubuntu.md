how-to-configure-network-ubuntu.md

### Configure network using init networking (text mode)

To configure networking with text mode, first you need to remove networking config from network-manager (GUI)


        /etc/network/interfaces

12.04 or 13.04 

		$ sudo /etc/init.d/networking restart 

13.10
   
        $ sudo service networking restart 

Normally dns-* option in /etc/resolv.conf can be configured in
/etc/network/interfaces. Don't need to specify it in /etc/resolvconf/resolv.conf.d 

		dns-search
		dns-nameserver
		dns-nameservers

/etc/resolv.conf is an symlink to /run/resolvconf/resolv.conf
or /etc/resolvconf/run/resolv.conf

note: in resolv.conf, do use "search" instead of "domain" option
because the search feature supersedes the domain feature. Unlike domain, search accepts multiple arguments. 

Once configured in /etc/network/interfaces, use resolvconf tool

		$ man resolvconf
		$ resolvconf -u 

If /etc/resolv.conf doesn't look right, maybe the resolvconf interface database needs to be updated by -d (delete) and -a (add) arguments

		$ resolvconf -d eth0
		$ ls /etc/resolvconf/run/interface       # you shouldn't see eth0 file
		$ resolvconf -a eth0
		$ ls /etc/resolvconf/run/interface       # now you should see the file and its content matched yours

In otherwords, to update the database you have to call resolvconf with the -a or -d option. 
That happens behind the scenes when you run ifup or ifdown. 
So, normally, as with any other change to /etc/network/interfaces, to activate changes to 
the dns-* options you have to ifdown the interface in question and ifup it again. Or you can reboot.

[referrence](http://askubuntu.com/questions/224966/how-do-i-get-resolvconf-to-regenerate-resolv-conf-after-i-change-etc-network-in)

*** If you don't want resolv.conf to take effect, make /etc/resolv.conf a regular file ***

#### check if resolvconf install

		$ dpkg --list | grep resolv

if not installed

		$ sudo apt-get install resolvconf

add config in /etc/resolvconf/resolv.conf.d/head

agian, /etc/resolv.conf is an symlink to /run/resolvconf/resolv.conf
or /etc/resolvconf/run/resolv.conf


### Configure network using network manager gui (graphic mode)

restart all interface

		$ sudo service network-manager restart

restart specific interface

		$ sudo service network-interface restart INTERFACE=eth0


### Caveat
There is a bug that if 2 network interfaces configured on network manager gui and/or 
/etc/network/interfaces, there will be confusion. Suggestion is to configure on 
/etc/network/interfaces on both interfaces. 

error message looks similar to this

		syslog.1:Oct  6 12:32:26 myhost NetworkManager[763]: <info> Unmanaged Device found; state CONNECTED forced. (see http://bugs.launchpad.net/bugs/191889)
		syslog.1:Oct  6 12:32:26 myhost NetworkManager[763]: <info> Activation (eth0) Beginning DHCPv4 transaction (timeout in 45 seconds)
		syslog.1:Oct  6 12:32:26 myhost NetworkManager[763]: <info> dhclient started with pid 14990
		syslog.1:Oct  6 12:32:26 myhost NetworkManager[763]: <info> Activation (eth0) Beginning IP6 addrconf.
		syslog.1:Oct  6 12:32:26 myhost NetworkManager[763]: <info> Activation (eth0) Stage 3 of 5 (IP Configure Start) complete.
		syslog.1:Oct  6 12:32:26 myhost NetworkManager[763]: <info> (eth0): DHCPv4 state changed nbi -> preinit
		syslog.1:Oct  6 12:32:46 myhost NetworkManager[763]: <info> (eth0): IP6 addrconf timed out or failed.
		syslog.1:Oct  6 12:32:46 myhost NetworkManager[763]: <info> Activation (eth0) Stage 4 of 5 (IP6 Configure Timeout) scheduled...
		syslog.1:Oct  6 12:32:46 myhost NetworkManager[763]: <info> Activation (eth0) Stage 4 of 5 (IP6 Configure Timeout) started...
		syslog.1:Oct  6 12:32:46 myhost NetworkManager[763]: <info> Activation (eth0) Stage 5 of 5 (IP Configure Commit) started...
		syslog.1:Oct  6 12:32:46 myhost NetworkManager[763]: <info> Activation (eth0) Stage 5 of 5 (IP Configure Commit) failed (no IP configuration found)

[link to bug](http://bugs.launchpad.net/bugs/191889)