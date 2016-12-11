# Configure users and security features

## Users

On the server, add a new user, and allow him to use `sudo`:

```shell
$ adduser theuser
$ gpasswd -a theuser sudo
```

Then, block `root` SSH access:

```shell
$ sudo nano /etc/ssh/sshd_config
# In the file, set "PermitRootLogin" to "no"
$ sudo service ssh restart
```

On your machine, create a SSH key for the user and add it on the server:

```shell
$ ssh-keygen -t rsa
$ ssh-copy-id ~/.ssh/the_key.pub user@host
```

## Firewall configuration

The firewall is [ufw](https://help.ubuntu.com/community/UFW), it comes preinstalled with Ubuntu.

We allow SSH, HTTP and HTTPS only.

```shell
$ sudo ufw allow ssh
$ sudo ufw allow 80/tcp
$ sudo ufw allow 443/tcp
$ sudo ufw enable
```

## fail2ban configuration

[fail2ban](http://www.fail2ban.org/) blocks bruteforcing attacks.

```shell
# Install the package
$ sudo apt-get install fail2ban iptables-persistent
# Copy the default configuration file
$ sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
# Edit the file, and set "enabled = true" for [ssh], in the #JAILS section 
$ sudo nano /etc/fail2ban/jail.local
```

The `ssh` section should look like this:

```shell
[ssh]

enabled  = true
port     = ssh
filter   = sshd
logpath  = /var/log/auth.log
maxretry = 6
```

Then, check the status of the service:

```shell
# Check if fail2ban is running
$ sudo /etc/init.d/fail2ban status
 * Status of authentication failure monitor
 *  fail2ban is running
# Check if the ssh jail is enabled
$ sudo fail2ban-client status
|- Number of jail:     1
`- Jail list:  		   ssh
```

Later, the logs can be checked:

```shell
# Open the default log file
$ sudo more /var/log/fail2ban.log
# Regroup the bans by IP
$ sudo awk '($(NF-1) = /Ban/){print $NF}' /var/log/fail2ban.log | sort | uniq -c | sort -n
```
