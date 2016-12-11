# Setup an empty virtual machine

This page describes the installation of an empty virtual machine, for local development.

Basically, you will get an empty Ubuntu instance, where all the server components will be installed.

* It should be accessible on `http://1.2.3.4` from the host (in an host-only network)
* It should have an Internet access, if available on the host
* It should share a folder with the host (this will be the `DOCUMENT_ROOT` of Apache)

---

* [Base installation](#base-installation)
* [Network setup](#network-setup)
* [Sharing folder setup](#sharing-folder-setup)
* [Console mode](#console-mode)

## Base installation

* Install [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
* Download a [Ubuntu ISO](https://www.ubuntu.com/download/desktop)
* Create an empty virtual machine
* In the VM settings, choose the ISO as CD
* Boot and follow the Ubuntu installation instructions
* Reboot the virtual machine
* In the VirtualBox menu, Devices, select _Insert Guest Additions CD images_, and follow the installation instructions
* Install OpenSSH: `sudo apt-get install openssh-server`

## Network setup

### In VirtualBox

In the network tab of the general VirtualBox settings:

* Add a new _host-only_ network or use an existing one (it should be named like `vboxnet0`)
* Set the IP to `1.2.3.1`, mask `255.255.255.0`
* Turn off the DHCP server (we won't need it)

In the VM settings, with the VM turned off:

* In the _Network_ settings:
    * In the Adapter 1, set a Host-only Adapter, and choose `vboxnet0` (this will be used to access the VM from the host)
    * In the Adapter 2, attach a NAT, and leave the advanced options as-is (this is needed if you want the VM to be on the same local network than the host, and have an Internet access)
* In the _System_ settings:
    * Check the Enable I/O APIC is checked (we need it for `vboxnet0` to work)

### In the virtual machine

Look for the newly created host-only adapter with `ifconfig` (it's the one without IP address; it should be named like `enp0s3`).

Open the network configuration file:

```bash
$ sudo nano /etc/network/interfaces
```

Adjust the file like below:

```
# This should already be here:
auto lo
iface lo inet loopback
# We add a static IP address for the host-only adapter (note the "enp0s3" name we have found before)
allow-hotplug enp0s3
iface enp0s3 inet static
address 1.2.3.4
```

Restart the network:

```bash
$ sudo ifdown enp0s3
$ sudo ifup enp0s3
```

Check the connection from the host with `ssh user@1.2.3.4`.

## Sharing folder setup

_The document root of the server stays on the host; we share it with the VM by using a VirtualBox shared folder._

In the VM settings, add a new shared folder, named `www`, linking to the root of the webserver on the host (with _Auto-mount_ and _Make permanent_ checked).

Then, in the VM:

```bash
# Add the user to the VirtualBox user group
$ sudo adduser $(whoami) vboxsf
# Also add the Apache user
$ sudo adduser www-data vboxsf
```

Logout or reboot the virtual machine. Then, the shared folder should be accessible in `/media/sf_www`.

## Console mode

_This is optional. For better performance, I don't want the Ubuntu UI to run. Why not using the Ubuntu Server edition then? I don't know_ :smile:

Edit the grub configuration:

```bash
$ sudo nano /etc/default/grub
```

* Comment `GRUB_CMDLINE_LINUX_DEFAULT` (it should be set to something like `quiet splash`)
* Set `GRUB_CMDLINE_LINUX` to `text` (instead of an empty string)
* Uncomment `GRUB_TERMINAL="console"`

Reload grub:

```bash
$ sudo update-grub
```

On Ubuntu 16, we also need to run:

```bash
$ sudo systemctl set-default multi-user.target
```

Restart the machine, it should open a console instead of the GUI.
