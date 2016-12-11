# Configure email support

## Mailcatcher installation (`dev` mode)

Mailcatcher is a fake SMTP server, that will catch the `mail()` calls from PHP, and output the results in a GUI.

```shell
# Install Ruby
$ sudo apt-get install ruby ruby-dev
# Install Mailcatcher
$ sudo gem install mailcatcher --no-ri --no-rdoc
# Launch Mailcatcher (todo: launch the service when the machine boots)
$ mailcatcher --ip=0.0.0.0
```

## Sendmail installation (`prod` mode)

> :warning: This section is a WIP, the `hosts` configuration (and my `sendmail` understanding) are not ideal at all.

Install sendmail:

```shell
$ sudo apt-get install sendmail
```

Update `/etc/hosts` by adding:

```
127.0.0.1       digitalocean.com localhost.localdomain localhost
127.0.1.1       digitalocean.com [HOSTNAME].localhost [HOSTNAME]
```

_A few informations about that:_

* [HOSTNAME] is the name returned by the `hostname` command_
* `digitalocean.com` will be present in the headers of each email sent
* We use it instead of a real domain name, because we are on a multi-domain environment
  * If we try to add several domains, sendmail will just not send anything
  * If we add only one domain, all other domains will use it in their headers

Update `/etc/mail/trusted-users` by adding:

```
www-data
```

Update `/etc/mail/submit.mc` by adding:

```
define(`_USE_CT_FILE_’,`1′)dnl
define(`confCT_FILE’,`/etc/mail/trusted-users’)dnl
define(`confRECEIVED_HEADER', `internal info suppressed')dnl
```

_Without that, we will get a `X-Authentication-Warning` warning in each email, saying that `www-data` is trying to force a sender with the `-f` option (this is done in `php.ini`)._

Launch the configuration tool *(and say Yes to everything)*:

```shell
$ sudo sendmailconfig
```

Then, try to send an email from the command line:

```shell
$ sendmail -f from@domain1.com to@domain2.com < mail.txt
```

And in PHP *(from Apache)*:

```php
// The "From" field should always be present
echo mail('to@domain2.com', 'Subject', 'Message', 'From: from@domain1.com <from@domain1.com>') ? 'ok' : 'ko';
```

After a few minutes, if nothing has been sent, check:

* If something is returned by the `sendmail -bp` command
* If an error has been written in `/var/mail/*`
* If an error has been written in `/var/log/mail.err`
* If a message has been written in `/var/log/mail.log` (like a [greylisting answer](https://en.wikipedia.org/wiki/Greylisting) received from the recipient)

Check also the email headers. It should not contain references to the real domain, only `digitalocean.com`. _(This is kind of ugly, but I didn't find an easy solution to easily support multi-domains)_
