# -*- mode: ruby -*-
# vi: set ft=ruby :
# localserver on vagrant http://ilyar.github.io/localserver/

# General project settings for customize
project_dir = File.basename(File.dirname(__FILE__))
project_name = File.basename(project_dir)
project_host = "#{project_name}.local"
project_host_alias = "mhp-test.mocotms.local"
#TODO custom IP Address

# Environment variables
ARCH = ENV['VM_ARCH'] || 32
MEMORY = ENV['VM_MEMORY'] || 1024
CORES = ENV['VM_CORES'] || 2

# Development script any command for run `vagrant provision`
$server_script = <<SCRIPT

SCRIPT

# Setup once App
$setup_app = <<SCRIPT

SCRIPT

# Check script
$check = <<SCRIPT

lsb_release -a
apachectl -V
php -v
php -m
mysql --version
phing -version
java -version
git --version
mysql --version
phing -version
composer --version
echo node $(node -v)
echo npm $(npm -v)
echo bower $(bower -v)

SCRIPT

# Setup script
$setup = <<SCRIPT

# Update the server
sudo add-apt-repository ppa:git-core/ppa -y
sudo add-apt-repository ppa:ondrej/php5-5.6 -y
sudo apt-get update && sudo apt-get upgrade -y --fix-missing

# Install basic tools
sudo apt-get install -y build-essential binutils-doc git curl htop

# Install Apache
sudo apt-get install -y apache2

## Configure Apache
echo "ServerName localhost" | sudo tee /etc/apache2/conf-available/fqdn.conf #TODO
sudo ln -sf /etc/apache2/conf-available/fqdn.conf /etc/apache2/conf-enabled/fqdn.conf
sudo a2enmod rewrite

## Configure VM user
sudo adduser vagrant www-data

# Install PHP (iconv, mbstring, openssl, tokenizer, ctype, pcre, etc) TODO docs
sudo apt-get install -y php5 php5-cli

## PHP modules: xmlrpc, curl, xsl, intl
sudo apt-get install -y php5-xmlrpc php5-curl php5-xsl php5-intl

## Install APC
sudo apt-get install -y php5-apcu

## Install PEAR
sudo apt-get install -y php-pear

## Display errors On
sudo sed -i "s/display_startup_errors = Off/display_startup_errors = On/g" /etc/php5/apache2/php.ini /etc/php5/cli/php.ini
sudo sed -i "s/display_errors = Off/display_errors = On/g" /etc/php5/apache2/php.ini /etc/php5/cli/php.ini

## Short opentag On
sudo sed -i "s/short_open_tag = Off/short_open_tag = On/g" /etc/php5/apache2/php.ini /etc/php5/cli/php.ini

## Configure xdebug
sudo apt-get install -y php5-xdebug
echo "xdebug.remote_enable=on
xdebug.remote_connect_back=on
xdebug.idekey=IDE" | sudo tee /etc/php5/apache2/conf.d/21-xdebug.ini /etc/php5/cli/conf.d/21-xdebug.ini

## Set timezone
echo "date.timezone=Etc/GMT" | sudo tee /etc/php5/apache2/conf.d/20-date.ini /etc/php5/cli/conf.d/20-date.ini

# Install MySQL
echo mysql-server     mysql-server/root_password        password local | sudo debconf-set-selections
echo mysql-server     mysql-server/root_password_again  password local | sudo debconf-set-selections
sudo apt-get -y install mysql-client mysql-server

## Allow MySQL root access from any host
sudo sed -i 's/^bind-address[[:blank:]]*=[[:blank:]]*127.0.0.1$/bind-address = 0.0.0.0/g' /etc/mysql/my.cnf
echo "GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'local' WITH GRANT OPTION" | mysql -u root --password=local
echo "GRANT PROXY ON ''@'' TO 'root'@'%' WITH GRANT OPTION" | mysql -u root --password=local

## Install phpmyadmin
echo phpmyadmin       phpmyadmin/reconfigure-webserver  text     apache2     | sudo debconf-set-selections
echo phpmyadmin       phpmyadmin/dbconfig-install       boolean  true        | sudo debconf-set-selections
echo phpmyadmin       phpmyadmin/app-password-confirm   password local       | sudo debconf-set-selections
echo phpmyadmin       phpmyadmin/mysql/admin-pass       password local       | sudo debconf-set-selections
echo phpmyadmin       phpmyadmin/password-confirm       password local       | sudo debconf-set-selections
echo phpmyadmin       phpmyadmin/setup-password         password local       | sudo debconf-set-selections
echo phpmyadmin       phpmyadmin/mysql/app-pass         password local       | sudo debconf-set-selections
sudo apt-get -y install phpmyadmin

# Install NodeJs anb tools
echo "Install NodeJs anb tools..."
curl -sL https://deb.nodesource.com/setup | bash -
apt-get install -yq nodejs
npm install -gq bower

# Install composer
curl -sS https://getcomposer.org/installer | php && mv composer.phar /usr/local/bin/composer
sudo -u vagrant -H composer --no-ansi -q global require "fxp/composer-asset-plugin:1.0.0-beta3"   # Install NPM/Bower Dependency Manager for Composer
sudo -u vagrant -H composer --no-ansi -q global require fabpot/php-cs-fixer @stable               # Install PHP Coding Standards Fixer
sudo -u vagrant -H echo export PATH=$PATH:/home/vagrant/.composer/vendor/bin >> .bashrc

# Phing Installation TODO via composer
sudo pear channel-discover pear.phing.info
sudo pear upgrade-all
sudo pear install -Z --alldeps phing/phing

# Installing OpenJDK 7
sudo apt-get install -y openjdk-7-jre

# Restart Services
sudo service mysql restart
sudo service apache2 restart

# Setup PATH
sudo -u vagrant -H echo "export PATH=/vagrant/bin:\$PATH" >>  /home/vagrant/.bashrc

SCRIPT

# Vagrant configuration
Vagrant.configure("2") do |config|

  # Define VM box to use
  config.vm.box = "ubuntu/precise#{ARCH}"
  config.vm.box_version = ">= 14.04"
  # TODO
  #config.vm.box_version = ">= 12.04" #tested on v20150512.0.1

  # TODO Setup Network
  config.vm.network "private_network", type: "dhcp"

  ## Options setup Network
  #config.vm.network :public_network
  #config.vm.network "public_network", bridge: "wlan0"
  #config.vm.network "public_network", bridge: "eth0"

  # TODO Setup VM
  
  config.vm.hostname = project_host
  config.vm.provider :virtualbox do |vm|
    vm.customize ["modifyvm", :id, "--memory", MEMORY.to_i]
    vm.customize ["modifyvm", :id, "--cpus", CORES.to_i]

    if CORES.to_i > 1
      vm.customize ["modifyvm", :id, "--ioapic", "on"]
    end
  end
  
  # Vagrant plugins
  
  ## Config vagrant-cachier
  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :box
  end

  ## TODO Config vagrant-hostmanager 
  if Vagrant.has_plugin?("vagrant-hostmanager")
    config.hostmanager.enabled = true
    config.hostmanager.manage_host = true
    config.hostmanager.aliases = project_host_alias.split(" ")
    config.hostmanager.ip_resolver = proc do |machine|
      result = ""
      machine.communicate.execute("ifconfig eth1") do |type, data|
        result << data if type == :stdout
      end
      (ip = /inet addr:(\d+\.\d+\.\d+\.\d+)/.match(result)) && ip[1]
    end
    config.vm.provision :hostmanager
  end

  # Prepare script run once at first start `vagrant up`
  config.ssh.shell = "bash -c 'BASH_ENV=/etc/profile exec bash'"

  if Dir.glob("#{File.dirname(__FILE__)}/.vagrant/machines/default/*/id").empty?
    config.vm.provision :shell, :inline => $setup, privileged: true
    config.vm.provision "app", type: "shell", inline: $setup_app, privileged: true
  end

  config.vm.provision :shell, :inline => $server_script
  config.vm.provision "check", type: "shell", inline: $check, privileged: true

end
