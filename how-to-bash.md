How to use bash shell (tips & tricks)
=====================================

#### List all cron jobs of all users in the system 
You would have to run this as root, but:

		$ for user in $(cut -f1 -d: /etc/passwd); do crontab -u $user -l; done

will loop over each user name listing out their crontab. The crontabs are owned by the respective users so you won't be able to see another user's crontab w/o being them or root.
--[edit] if you want to know, which user does a crontab belong to insert echo $user

		$ for user in $(cut -f1 -d: /etc/passwd); do echo $user; crontab -u $user -l; done


#### Make CPU high all the times
note: it reads stream of zeroes from /dev/zero and write into a null file /dev/null 
which basically gets discarded.

		$ dd if=/dev/zero of=/dev/null


#### Write a huge file until disk space is full.

		$ dd if=/dev/zero of=/tmp/hugefile

		To specify filesize
		$ dd if=/dev/zero of=/tmp/hugefile bs=700000000 count=1       ;# will write a 700MB file

#### Find out network connection usage

		$ lsof -i

		COMMAND    PID     USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
		dropbox   2749 songserm   24u  IPv4 743438      0t0  TCP songserm-ubt.local:32899->snt-re2-8c.sjc.dropbox.com:http (ESTABLISHED)
		dropbox   2749 songserm   29u  IPv4  15602      0t0  UDP *:17500 
		dropbox   2749 songserm   32u  IPv4  15605      0t0  TCP *:17500 (LISTEN)
		ubuntu-ge 2849 songserm    7u  IPv4  15155      0t0  TCP songserm-ubt.local:59924->mistletoe.canonical.com:http (ESTABLISHED)
		unity-sco 3046 songserm   12u  IPv4  15662      0t0  TCP songserm-ubt.local:54618->alkes.canonical.com:http (ESTABLISHED)
		unity-sco 3046 songserm   14u  IPv4  15663      0t0  TCP songserm-ubt.local:54619->alkes.canonical.com:http (ESTABLISHED)


		# small cheat sheet
		$ lsof -h

#### Find out memory/cpu usage

        # refress rate is 0.5 sec
		$ top -d 0.5 

		# list only this process id
		$ top -p 1234

		# htop is more interactive
		$ htop


#### Buffer & cache in Linux memory usage

The buffers number represents in-memory blocks that result from the kernel accessing the disk, such as when the kernel needs to read the contents of files. The cached figure tells us how much RAM is being used to cache the content of recently read files. The buffer figure increases when the file system layer is bypassed while the cache grows when the file system is used. Both grow as read operations increase.

For more details, you can visit this [link](http://www.itworld.com/it-managementstrategy/280695/making-sense-memory-usage-linux?page=0,2)

		top - 18:54:11 up 38 days, 19:58,  7 users,  load average: 0.02, 0.01, 0.00
		Tasks: 181 total,   1 running, 179 sleeping,   1 stopped,   0 zombie
		Cpu(s):  0.0%us,  0.0%sy,  0.0%ni, 99.8%id,  0.1%wa,  0.0%hi,  0.0%si,  0.0%st
		Mem:   2074952k total,  1967968k used,   106984k free,   503416k buffers
		Swap:  4192956k total,      128k used,  4192828k free,  1135640k cached

		  PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND
		18751 shs       15   0  2424  980  724 R  2.0  0.0   0:00.01 top
		    1 root      15   0  2160  592  516 S  0.0  0.0   0:01.07 init
		    2 root      RT  -5     0    0    0 S  0.0  0.0   0:00.07 migration/0
		    3 root      34  19     0    0    0 S  0.0  0.0   0:00.00 ksoftirqd/0
		    4 root      RT  -5     0    0    0 S  0.0  0.0   0:00.00 watchdog/0
		    5 root      RT  -5     0    0    0 S  0.0  0.0   0:00.77 migration/1
		    6 root      34  19     0    0    0 S  0.0  0.0   0:00.00 ksoftirqd/1
		    7 root      RT  -5     0    0    0 S  0.0  0.0   0:00.00 watchdog/1
		    8 root      RT  -5     0    0    0 S  0.0  0.0   0:00.14 migration/2
		    9 root      39  19     0    0    0 S  0.0  0.0   0:00.02 ksoftirqd/2
		   10 root      RT  -5     0    0    0 S  0.0  0.0   0:00.00 watchdog/2
		   11 root      RT  -5     0    0    0 S  0.0  0.0   0:00.32 migration/3
		   12 root      39  19     0    0    0 S  0.0  0.0   0:00.04 ksoftirqd/3
		   13 root      RT  -5     0    0    0 S  0.0  0.0   0:00.00 watchdog/3
		   14 root      10  -5     0    0    0 S  0.0  0.0   0:00.02 events/0
		   15 root      10  -5     0    0    0 S  0.0  0.0   0:00.00 events/1
		   16 root      10  -5     0    0    0 S  0.0  0.0   0:00.00 events/2


#### Generate a timestamp in various way

		$ date +"%Y%m%d%H%M%S"
		20131010102451

		$ date +"%Y-%m-%d %H:%M:%S"
		2013-10-10 10:24:34

		$ date --date='@1374000050' +"%Y-%m-%d %H:%M:%S"
		2013-07-17 01:40:50


#### Run a command on behalf on other user 

		$ su - chawarong -c 'whoami'
		$ su - chawarong -c 'cmd1 & cmd2'


#### Set vim as a default editor in ubuntu 

		$ sudo update-alternatives --set editor /usr/bin/vim.basic
	
#### Walk into each releases folder and do git status

        $ listdir=`ls`
        $ for l in $listdir ; do cd /var/www/my_rails_app/releases/$l; pwd; git status; done

        # look for modified deploy.rb 
        $ for l in $listdir ; do cd /var/www/my_rails_app/releases/$l; pwd; git status; done | grep deploy.rb

#### To remove a user completely along with their home directory

        $ sudo deluser --remove-home chawarong

#### To remove a service 

        $ sudo service --status-all
        $ cd /etc
        $ ll rc* | less                              ;# S is start, K is kill
        $ sudo update-rc.d -f nscd remove