# Install MySQL

```shell
# Install the package (set a strong password for root, when asked)
$ sudo apt-get install mysql-server
# Configure the installation ("yes" to all)
$ sudo mysql_secure_installation
```

Open `/etc/mysql/my.cnf` and uncomment the following line, to prevent external MySQL access:

```
[mysqld]
...
bind-address = 127.0.0.1
...
```

Then, check the users list:

```
$ mysql -u root -p
mysql> SELECT User,Host,authentication_string FROM mysql.user;
+------------------+-----------+-------------------------------------------+
| User             | Host      | authentication_string                     |
+------------------+-----------+-------------------------------------------+
| root             | localhost | *81F5E21E35407D884A6CD4A731AEBFB6AF209E1B |
| mysql.sys        | localhost | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
| debian-sys-maint | localhost | *C07993FF917158CE5612D617122441332D964D90 |
+------------------+-----------+-------------------------------------------+
```

No one should have remote access, or an empty password.
