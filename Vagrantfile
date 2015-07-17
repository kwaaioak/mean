# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
    config.vm.hostname = "local.mean.io"
    config.vm.box = "ubuntu/trusty64"

    # Parallels
    config.vm.provider "parallels"
    config.vm.provider :parallels do |v, override|
        v.name = "meanio"
        v.memory = 1024
        v.cpus = 1

        override.vm.box = "parallels/ubuntu-14.04"
    end

    # VirtualBox
    config.vm.provider "virtualbox"
    config.vm.provider :virtualbox do |v, override|
        v.name = "meanio"
        v.memory = 1024
        v.cpus = 1
    end

    # Networking
    begin
        config.vm.network :private_network, ip: Socket::getaddrinfo(config.vm.hostname, 'http', nil, Socket::SOCK_STREAM)[0][3]
    rescue SocketError
        $stderr.puts "Could not resolve " + config.vm.hostname + " (see README.md) - one will be assigned automatically"
    end

    # File sharing
    if RUBY_PLATFORM =~ /cygwin|mswin|mingw|bccwin|wince|emx/
        config.vm.synced_folder   "./cache/apt/archives", "/var/cache/apt/archives"
    else
        config.vm.synced_folder   "./cache/apt/archives", "/var/cache/apt/archives"
        config.vm.synced_folder   ".", "/vagrant"
        config.vm.provider :virtualbox do |v, override|
            override.vm.synced_folder   "./cache/apt/archives", "/var/cache/apt/archives", type: "nfs"
            override.vm.synced_folder   ".", "/vagrant", type: "nfs"
        end
    end

    # Ansible provisioning
    config.vm.provision :shell, inline: "test -x /usr/bin/ansible || ( sudo apt-get install -y software-properties-common && sudo apt-add-repository -y ppa:ansible/ansible && apt-get update && apt-get install -y software-properties-common ansible )"
#    config.vm.provider :virtualbox do |v, override|
        config.vm.provision :shell, inline: "{ test -d /vagrant/node_modules || ( mkdir -p /vagrant/node_modules ) }; { test -d /var/lib/meanio/node_modules || ( mkdir -p /var/lib/meanio/node_modules && chown vagrant:vagrant /var/lib/meanio/node_modules && mount --bind /var/lib/meanio/node_modules /vagrant/node_modules && echo \"/var/lib/meanio/node_modules /vagrant/node_modules none defaults,bind 0 0\" >>/etc/fstab ) }"
        config.vm.provision :shell, inline: "{ test -d /vagrant/bower_components || ( mkdir -p /vagrant/bower_components ) }; { test -d /var/lib/meanio/bower_components || ( mkdir -p /var/lib/meanio/bower_components && chown vagrant:vagrant /var/lib/meanio/bower_components && mount --bind /var/lib/meanio/bower_components /vagrant/bower_components && echo \"/var/lib/meanio/bower_components /vagrant/bower_components none defaults,bind 0 0\" >>/etc/fstab ) }"
        config.vm.provision :shell, inline: "{ test -d /vagrant/.bower-tmp || ( mkdir -p /vagrant/.bower-tmp ) }; { test -d /var/lib/meanio/.bower-tmp || ( mkdir -p /var/lib/meanio/.bower-tmp && chown vagrant:vagrant /var/lib/meanio/.bower-tmp && mount --bind /var/lib/meanio/.bower-tmp /vagrant/.bower-tmp && echo \"/var/lib/meanio/.bower-tmp /vagrant/.bower-tmp none defaults,bind 0 0\" >>/etc/fstab ) }"
        config.vm.provision :shell, inline: "{ test -d /vagrant/.bower-cache || ( mkdir -p /vagrant/.bower-cache ) }; { test -d /var/lib/meanio/.bower-cache || ( mkdir -p /var/lib/meanio/.bower-cache && chown vagrant:vagrant /var/lib/meanio/.bower-cache && mount --bind /var/lib/meanio/.bower-cache /vagrant/.bower-cache && echo \"/var/lib/meanio/.bower-cache /vagrant/.bower-cache none defaults,bind 0 0\" >>/etc/fstab ) }"
        config.vm.provision :shell, inline: "{ test -d /vagrant/.bower-registry || ( mkdir -p /vagrant/.bower-registry ) }; { test -d /var/lib/meanio/.bower-registry || ( mkdir -p /var/lib/meanio/.bower-registry && chown vagrant:vagrant /var/lib/meanio/.bower-registry && mount --bind /var/lib/meanio/.bower-registry /vagrant/.bower-registry && echo \"/var/lib/meanio/.bower-registry /vagrant/.bower-registry none defaults,bind 0 0\" >>/etc/fstab ) }"
#    end
    config.vm.provision :shell, privileged: false, keep_color: true, inline: "PYTHONUNBUFFERED=1 ANSIBLE_FORCE_COLOR=true ansible-playbook -i /vagrant/ansible/inventories/vagrant/hosts /vagrant/ansible/site.yml"

    config.vm.post_up_message = "MeanIO server is running - open http://#{config.vm.hostname}"
end
