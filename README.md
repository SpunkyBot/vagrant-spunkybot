# Vagrant file for Spunky Bot

Vagrant virtual machine with Spunky Bot service running

## Requirements

You need a recent version of

* [Vagrant](https://www.vagrantup.com/downloads.html)
* [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
* [Urban Terror](http://www.urbanterror.info/downloads/)

Per default Urban Terror is expected to be installed in `C:\UrbanTerror43`.
Otherwise you need to modify the path in the `Vagrantfile` before starting the virtual appliance.

## Installation

These steps will create a Vagrant virtual machine for VirtualBox with Spunky Bot service running:

```shell
$ wget https://raw.githubusercontent.com/SpunkyBot/vagrant-spunkybot/main/Vagrantfile
$ vagrant up
```

## Usage

After starting the virtual machine, you need to start the Urban Terror und Spunky Bot instance manually:

```shell
$ sudo service urbanterror start
$ sudo service spunkybot start
```

You can reach the game server by pointing your client to: `/connect 127.0.0.1:27970`

The default `rconpassword` is: _secretpassword_

PRISM, the web frontend for Spunky Bot, is reachable via your web browser: `http://127.0.0.1`

We are shipping this virtual machine appliance with a basic setup to run the Urban Terror server with support of the latest Spunky Bot and PRISM version. Please feel free to configure the setup to your needs.
