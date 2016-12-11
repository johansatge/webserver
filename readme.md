![LAMP logos](logos.png)

# Webserver

This document describes the installation of a basic LAMP server, based on an empty [Ubuntu](http://www.ubuntu.com/) instance.

## Features

* Apache 2.4
* PHP 7.1
* MySQL 5.7
* SSL support with [Let's Encrypt](https://letsencrypt.org/)
* Email support
* Multi-domain support

## Installation

* [Setup an empty virtual machine](virtual-machine.md) (for `dev` mode), or start from an empty VPS
* [Configure users and security features](users-and-security.md)
* [Install Apache](apache.md)
* [Install PHP](php.md)
* [Install MySQL](mysql.md)
* [Configure SSL support](ssl.md)
* [Configure email support](mail.md)
* [Install an application](install-app.md)

## Philosophy

The server can run in two modes:

* `dev`, in a local virtual machine (as a [MAMP](https://www.mamp.info/en/) replacement, for instance)
* `prod`, somewhere on the Internet (like a DigitalOcean droplet)

There are almost no differences between the two, but:

* A few Apache parameters (look for `<If>` conditions in [apache2.conf](conf/apache2.conf))
* The SSL support (in `dev` mode, Let's Encrypt is not used)
* The mailing support (in `dev` mode, [Mailcatcher](https://mailcatcher.me) is used instead of `sendmail`)
