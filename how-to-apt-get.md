how-to-apt-get.md

#### To search for available packages

	$ apt-cache search [name-pattern]
	$ apt-cache search --names-only [name-pattern]

#### How to see the file path location of the packages

	$ dpkg -L [package]

#### To remove packages

	$ apt-get remove

remove is identical to install except that packages are removed instead of installed. Note the removing a package leaves its configuration files in system. If a plus sign is appended to the package name (with no intervening space), the identified package will be installed instead of removed.

	$ apt-get purge

purge is identical to remove except that packages are removed and purged (any configuration files are deleted too).

	$ apt-get remove --purge

remove --purge is identical to apt-get purge
    
    $ apt-get autoremove

autoremove is used to remove packages that were automatically installed to satisfy dependencies for other packages and are now no longer needed.

#### example of remove packages

	# remove mysql completely
	$ sudo apt-get remove --purge mysql-server mysql-client mysql-common
	$ sudo apt-get autoremove
	$ sudo apt-get autoclean

	# remove nginx complete including configurations
	$ sudo apt-get purge nginx-full nginx-common