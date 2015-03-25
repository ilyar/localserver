# [localserver on vagrant](http://ilyar.github.io/localserver/)

Make it very easy to setup a complete development stack (MySQL, PHP, NodeJS, etc) in a matter of minutes.

## Requirements

* VirtualBox <http://www.virtualbox.com>
* Vagrant <http://www.vagrantup.com> and optional plugins:
 * [vagrant-hostmanager](https://github.com/smdahlen/vagrant-hostmanager) `vagrant plugin install vagrant-hostmanager`
 * [vagrant-cachier](http://fgrehm.viewdocs.io/vagrant-cachier) `vagrant plugin install vagrant-cachier`

## Usage

Download [Vagrantfile](http://ilyar.github.io/localserver/Vagrantfile) to `project_name_dir` and up server

	$ cd project_name_dir
	$ wget http://ilyar.github.io/localserver/Vagrantfile
	$ vagrant up

The project is available at `http://project_name_dir.local/`

Note:

	$ vagrant suspend # suspends the machine
	$ vagrant halt # stops the vagrant machine
	$ vagrant destroy -f # stops and deletes all traces of the vagrant machine


And like any other vagrant file you have SSH access with:

	$ vagrant ssh
	
also can run once command:

	$ ssh vagrant@localserver composer update

## Docs for setup IDE

* [PHP Remote Interpreters Support in PhpStorm 8 EAP](http://blog.jetbrains.com/phpstorm/2014/04/php-remote-interpreters-support-in-phpstorm-8-eap/)
* [How to Install Xdebug with PHPStorm and Vagrant](http://www.sitepoint.com/install-xdebug-phpstorm-vagrant/) read from `Configuring PHPStorm – Servers` 
* [Working with Advanced Vagrant features in PhpStorm](http://confluence.jetbrains.com/display/PhpStorm/Working+with+Advanced+Vagrant+features+in+PhpStorm)

## Technical Details

* Ubuntu 14.04 LTS 32-bit (for use 64-bit set `VM_ARCH=64`)
* Apache 2
* MySQL 5.5
* PHP 5.5 (with modules: apc, memcached, cli, curl, gd, imap, mysql, mcrypt, sqlite, xdebug, xsl, pear, intl, tidy, readline) and tools:
 * phpmyadmin <http://localserver/phpmyadmin/>
 * [composer](https://getcomposer.org/) — Dependency Manager for PHP
 * [composer-asset-plugin](https://github.com/francoispluchino/composer-asset-plugin/blob/master/Resources/doc/index.md) — NPM/Bower Dependency Manager for Composer
 * [php-cs-fixer](https://github.com/fabpot/PHP-CS-Fixer#usage) — PHP Coding Standards Fixer
* NodeJs (node, npm, bower)

We are using the base Ubuntu 14.04 box from Vagrant. If you don't already have it downloaded
the Vagrantfile has been configured to do it for you. This only has to be done once
for each account on your host computer.

The web root is located in the project directory and you can install your files there.

## Default credentials

### MySQL

Externally the MySQL server is available at `localserver:3306`, and when running on the VM it is available as a socket or at port 3306 as usual.
Username: root
Password: local

### Project settings for customize in `Vagrantfile`

```bash
custom_project_name  = false # default from project name folder
custom_project_host  = false # default `project_name.local`
custom_project_alias = false # default `www.project_name.local`

custom_ip_address    = false # default "10.0.0.10" IPv4 private network range
custom_default_host  = false # default "localserver"

custom_vm_arch       = false # default 32  or VM_ARCH
custom_vm_memory     = false # default 512 or VM_MEMORY
custom_vm_cores      = false # default 1   or VM_CORES
```
