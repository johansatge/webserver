# Install Apache

```shell
# Install the package
$ sudo apt-get install apache2
# Save the original config file
$ sudo mv /etc/apache2/apache2.conf /etc/apache2/apache2.orig.conf
# Add an environment variable in /etc/apache2/envvars, with the desired type: dev or prod
# This var is used in apache2.conf
$ echo "export APACHE_ENV='dev'" | sudo tee -a /etc/apache2/envvars
# Add an environment variable in /etc/apache2/envvars, with the path of the document root
# (/media/sf_www or /var/www)
# This will be used in the virtual hosts file
$ echo "export APACHE_DOCUMENT_ROOT='/media/sf_www'" | sudo tee -a /etc/apache2/envvars
```

Add [apache2.conf](conf/apache2.conf) in `/etc/apache2`:

```shell
$ curl https://raw.githubusercontent.com/johansatge/webserver/master/conf/apache2.conf > /tmp/apache2.conf
$ sudo mv /tmp/apache2.conf /etc/apache2/apache2.conf
```

Add [vhosts.default.conf](conf/vhosts.default.conf) in `/etc/apache2`:

```shell
$ curl https://raw.githubusercontent.com/johansatge/webserver/master/conf/vhosts.default.conf > /tmp/vhosts.default.conf
$ sudo mv /tmp/vhosts.default.conf /etc/apache2/vhosts.conf
```
