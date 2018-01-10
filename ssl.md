# Configure SSL support

Generate a self-signed certificate for the default virtual host, in `/self-ssl`:

```bash
$ sudo mkdir /self-ssl
$ sudo chmod 700 /self-ssl
$ sudo openssl req -x509 -nodes -days 3650 -newkey rsa:2048 -keyout /self-ssl/ssl.key -out /self-ssl/ssl.crt
$ sudo chmod 600 /self-ssl/ssl.crt
$ sudo chmod 600 /self-ssl/ssl.key
```

Install the [certbot](https://certbot.eff.org/) helper from Let's Encrypt:

```bash
$ sudo apt-get update
$ sudo apt-get install software-properties-common
$ sudo add-apt-repository ppa:certbot/certbot
$ sudo apt-get update
$ sudo apt-get install certbot
```

Configure a cron task to renew automatically all the registered domains:

```bash
$ sudo crontab -e
# Add the new line below
# The renewal will be triggered every day at 2am
0 2 * * * certbot renew --no-self-upgrade
```

Logs can be viewed in `/var/log/letsencrypt/letsencrypt.log`.
