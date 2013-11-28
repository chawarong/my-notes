how-to-mysql-reset-root-password.md

### Reset the MySQL root password on Ubuntu Linux. 

Enter the following lines in your terminal.

1. Stop the MySQL Server.

    $ sudo /etc/init.d/mysql stop


2. Start the mysqld configuration.

    $ sudo mysqld --skip-grant-tables &


3. Login to MySQL as root.

    $ mysql -u root mysql


4. Replace YOURNEWPASSWORD with your new password!

    mysql> UPDATE user SET Password=PASSWORD('YOURNEWPASSWORD') WHERE User='root'; FLUSH PRIVILEGES; exit;

5. To drop a 'root'@'%'

        mysql> drop user 'root'@'%';


Note: This method is not regarded as the securest way of resetting the password. However it works.


References

MySQL 5.0 Reference Manual: How to Reset the Root Password (http://dev.mysql.com/doc/refman/5.0/en/resetting-permissions.html)

#### Tips
If you can't read unicode characters (it appears as in ???). Use this command

    mysql> SET NAMES utf8

### Update root password 

    $ mysqladmin -u root -p'oldpassword' password newpassword