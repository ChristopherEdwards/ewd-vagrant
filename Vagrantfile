# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "ubuntu/xenial64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  config.vm.network "forwarded_port", guest: 8080, host: 8080

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.

  # the script below in influence by:
  # https://github.com/robtweed/ewd-3-installers/blob/master/ewd-xpress/install_gtm.sh
  # https://github.com/OSEHRA/VistA/blob/master/Scripts/Install/EWD/ewdjs.sh
  config.vm.provision "shell", inline: <<-SHELL
     echo "updating ubuntu and installing required packages"
     export DEBIAN_FRONTEND="noninteractive"
     apt-get update -qq
     apt-get install -y -qq build-essential
     apt-get install -y -qq wget gzip openssh-server curl python
     # Install gtm
     pushd /tmp
     curl -s --remote-name -L https://sourceforge.net/projects/fis-gtm/files/GT.M%20Installer/v0.13/gtminstall
     chmod +x gtminstall
     ./gtminstall --utf8 default
     gtmver=$(ls -1 /usr/lib/fis-gtm)
     echo "source /usr/lib/fis-gtm/$gtmver/gtmprofile" >> /home/ubuntu/.profile
     ln -s $gtm_dist/libgtmshr.so /vagrant/lib
     ldconfig
     rm gtminstall
     # Install node.js
     curl -s -k --remote-name -L https://raw.githubusercontent.com/creationix/nvm/v0.31.0/install.sh
     chmod +x install.sh
     su ubuntu -c ./install.sh > /dev/null 2>&1
     echo 'source ~/.nvm/nvm.sh' >> /home/ubuntu/.profile
     su ubuntu -c "source /home/ubuntu/.profile && nvm alias default 4.4 && nvm install 4.4 && nvm use default"
     echo 'nvm use default' >> /home/ubuntu/.profile
     rm install.sh
     popd
     # Install ewd3
     mkdir -p /vagrant/ewd3
     chown -R ubuntu:ubuntu /vagrant/ewd3
     su ubuntu -c "source /home/ubuntu/.profile && pushd /vagrant/ewd3 && npm install ewd-xpress ewd-xpress-monitor nodem"
     # Configure nodem
     echo 'export GTMCI=$(find /vagrant/ewd3 -iname nodem.ci)' >> /home/ubuntu/.profile
     echo 'nodemgtmr=$(find /vagrant/ewd3 -iname v4wnode.m | tail -n1 | xargs dirname)' >> /home/ubuntu/.profile
     echo 'echo $gtmroutines | fgrep $nodemgtmr || export gtmroutines="$nodemgtmr $gtmroutines"' >> /home/ubuntu/.profile
     # Configure ewd-xpress
     su ubuntu -c "cp /vagrant/ewd3/node_modules/ewd-xpress/example/ewd-xpress-gtm.js /vagrant/ewd3"
     mkdir -p /vagrant/ewd3/www/ewd-xpress-monitor
     chown -R ubuntu:ubuntu /vagrant/ewd3
     su ubuntu -c "cp /vagrant/ewd3/node_modules/ewd-xpress-monitor/www/bundle.js /vagrant/ewd3/www/ewd-xpress-monitor"
     su ubuntu -c "cp /vagrant/ewd3/node_modules/ewd-xpress-monitor/www/*.html /vagrant/ewd3/www/ewd-xpress-monitor"
     su ubuntu -c "cp /vagrant/ewd3/node_modules/ewd-xpress-monitor/www/*.css /vagrant/ewd3/www/ewd-xpress-monitor"
     chown -R ubuntu:ubuntu /vagrant/ewd3
     echo 'EWD-Xpress installed!'
     echo 'to start ewd3 run the following commands when logged in'
     echo 'pushd /vagrant/ewd3 && node ewd-xpress-gtm.js && popd'
   SHELL
end
