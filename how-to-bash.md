How to use bash shell (tips & tricks)
=====================================

#### List all cron in the system
You would have to run this as root, but:

		$ for user in $(cut -f1 -d: /etc/passwd); do crontab -u $user -l; done

will loop over each user name listing out their crontab. The crontabs are owned by the respective users so you won't be able to see another user's crontab w/o being them or root.
--[edit] if you want to know, which user does a crontab belong to insert echo $user

		$ for user in $(cut -f1 -d: /etc/passwd); do echo $user; crontab -u $user -l; done


#### Make CPU high all the times
note: it reads stream of zeroes from /dev/zero and write into a null file /dev/null 
which basically gets discarded.

		$ dd if=/dev/zero of=/dev/null


#### Write a file huge file until disk is full.

		$ dd if=/dev/zero of=/tmp/hugefile