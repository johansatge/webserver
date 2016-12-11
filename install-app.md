# Install an application

## Install the `http` app

* Create a new directory, `thedomain.com`, in the `APACHE_DOCUMENT_ROOT` directory (see [Install Apache](apache.md))
* Install the app files
* Add a new virtual host configuration in `/etc/apache2/vhosts.conf`:

```apache
<VirtualHost *:80>
    ServerName thedomain.com
    ServerAlias www.thedomain.com
</VirtualHost>
```

Restart Apache:

```shell
$ sudo service apache2 restart
```

> :bulb: We need to install a basic, `HTTP` version of the app first, to make the domain run. Let's Encrypt needs it in order to check its ownership when requesting the certificate (see [webroot docs](http://letsencrypt.readthedocs.io/en/latest/using.html#webroot)).

## Get an SSL certificate

Get the certificate:

```shell
# Add a -d option for each needed subdomain
$ sudo /certbot/certbot-auto certonly --webroot -w /var/www/thedomain.com -d thedomain.com -d www.thedomain.com
```

_This will also automatically register the domain for renewal, in our cron task._

## Configure the `https` app

Now we have a valid certificate, we can enable the `https` virtual host.

Add a new virtual host in `etc/apache2/vhosts.conf`:

```apache
<VirtualHost *:443>
    ServerName thedomain.com
    ServerAlias www.thedomain.com

    ServerAdmin noreply@thedomain.com
    DocumentRoot ${APACHE_DOCUMENT_ROOT}/thedomain.com

    # Redirect non-www calls to www
    <If "req('Host') != 'www.thedomain.com'">
        RedirectMatch (.*) https://www.thedomain.com$1
    </If>

    # Enable SSL
    SSLEngine on
    SSLCertificateFile /etc/letsencrypt/live/thedomain.com/cert.pem
    SSLCertificateKeyFile /etc/letsencrypt/live/thedomain.com/privkey.pem
    SSLCertificateChainFile /etc/letsencrypt/live/thedomain.com/chain.pem
</VirtualHost>
```

Add a redirection rule to `https` in the `http` host we have previously created:

```diff
<VirtualHost *:80>
    ServerName thedomain.com
    ServerAlias www.thedomain.com
+   RedirectMatch (.*) https://thedomain.com$1
</VirtualHost>
```

Restart Apache again.
