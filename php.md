# Install PHP

```shell
# Install the packages
$ sudo add-apt-repository ppa:ondrej/php
$ sudo apt-get update
$ sudo apt-get install php7.2
$ sudo apt-get install php7.2-mysql
$ sudo apt-get install php7.2-curl
$ sudo apt-get install php7.2-gd
# Save the original config file
$ sudo mv /etc/php/7.2/apache2/php.ini /etc/php/7.2/apache2/php.orig.ini
```

Add [php.ini](conf/php.ini) in `/etc/php/7.2/apache2`:

```shell
$ curl https://raw.githubusercontent.com/johansatge/webserver/master/conf/php.ini > /tmp/php.ini
$ sudo mv /tmp/php.ini /etc/php/7.2/apache2/php.ini
```
