# -*- mode: ruby -*-
# vi: set ft=ruby :

## == required plugins and params == ##
# vagrant plugin install vagrant-hosts
# vagrant plugin install vagrant-cachier
# gem install ruby-libvirt
# gem install fog
# vagrant plugin install vagrant-libvirt
# export VAGRANT_DEFAULT_PROVIDER="libvirt"
##

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "vincent-box"
  config.vm.box_url = "http://cotocisternas.cl/public/files/vagrant/veewee/vincent-box_kvm.box"
  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.auto_detect = true
  end

  config.vm.provider :libvirt do |kvm|
    kvm.driver = 'kvm'
    kvm.memory = 1024
    kvm.cpus   = 2
    #kvm.storage_pool_name = 'default'
    #kvm.storage_pool_name = 'storage'
  end

  config.vm.define "puppet" do |master|
    master.vm.hostname = "tx-puppet01-zz1.txel.systems"
    master.vm.network :private_network, ip: "172.16.210.10"
    master.vm.provision :hosts do |provisioner|
      provisioner.autoconfigure = false
      provisioner.add_host '127.0.0.1', ['tx-puppet01-zz1.txel.systems', 'tx-puppet01-zz1', 'puppet']
    end
    master.vm.provision :shell, :inline => "echo -e 'deb http://ftp.cl.debian.org/debian wheezy main non-free contrib\ndeb-src http://ftp.cl.debian.org/debian wheezy main non-free contrib\n\ndeb http://ftp.cl.debian.org/debian-security/ wheezy/updates main contrib non-free\ndeb-src http://ftp.cl.debian.org/debian-security/ wheezy/updates main contrib non-free' > /etc/apt/sources.list"
    master.vm.provision :shell, :inline => "apt-get update && apt-get -y dist-upgrade"
    master.vm.provision :shell, :path => "master_conf/puppet_master.sh"
    master.vm.provision :shell, :path => "master_conf/puppet_r10k.sh"
    master.vm.provision :puppet do |puppet|
      puppet.manifests_path = "master_conf/manifests"
      puppet.manifest_file  = "default.pp"
      puppet.options        = "--verbose --modulepath /home/vagrant/modules"
    end
    master.vm.synced_folder "puppet/manifests", "/etc/puppet/manifests", type: '9p'
    master.vm.synced_folder "puppet/modules", "/etc/puppet/modules", type: '9p'
    master.vm.synced_folder "puppet/hieradata", "/etc/puppet/hieradata", type: '9p'
    master.vm.provider :libvirt do |kvm|
      kvm.memory  = 4096
      kvm.cpus    = 4
    end
  end

  nodes = {
    :dns01  => {:host => 'tx-dns01-zz1',            :domain => 'txel.systems', :ip => '172.16.210.2'                },
    :mon01  => {:host => 'tx-mon01-zz1',            :domain => 'txel.systems', :ip => '172.16.210.5',  :mem => 6144 },
    :git01  => {:host => 'tx-git01-zz1',            :domain => 'txel.systems', :ip => '172.16.210.6',  :mem => 4096 },
    :up01   => {:host => 'tx-status01-zz1',         :domain => 'txel.systems', :ip => '172.16.210.15'               },
    :web01  => {:host => 'au-dec5-web01-zz1',       :domain => 'txel.systems', :ip => '172.16.210.31'               },
    :web02  => {:host => 'au-dec5-web02-zz1',       :domain => 'txel.systems', :ip => '172.16.210.32'               },
    :web03  => {:host => 'au-dec5-web03-zz1',       :domain => 'txel.systems', :ip => '172.16.210.33'               },
    :sql01  => {:host => 'au-dec5-percona01-zz1',   :domain => 'txel.systems', :ip => '172.16.210.41'               },
    :sql02  => {:host => 'au-dec5-percona02-zz1',   :domain => 'txel.systems', :ip => '172.16.210.42'               },
    :sql03  => {:host => 'au-dec5-percona03-zz1',   :domain => 'txel.systems', :ip => '172.16.210.43'               },
    :app01  => {:host => 'au-dec5-app01-zz1',       :domain => 'txel.systems', :ip => '172.16.210.51'               },
    :app02  => {:host => 'au-dec5-app02-zz1',       :domain => 'txel.systems', :ip => '172.16.210.52'               },
    :app03  => {:host => 'au-dec5-app03-zz1',       :domain => 'txel.systems', :ip => '172.16.210.53'               },
    :es01   => {:host => 'au-dec5-elastic01-zz1',   :domain => 'txel.systems', :ip => '172.16.210.61'               },
    :es02   => {:host => 'au-dec5-elastic02-zz1',   :domain => 'txel.systems', :ip => '172.16.210.62'               },
    :es03   => {:host => 'au-dec5-elastic03-zz1',   :domain => 'txel.systems', :ip => '172.16.210.63'               },
    :web11  => {:host => 'au-dec4-web01-zz1',       :domain => 'txel.systems', :ip => '172.16.210.71'               },
    :web12  => {:host => 'au-dec4-web02-zz1',       :domain => 'txel.systems', :ip => '172.16.210.72'               },
    :web13  => {:host => 'au-dec4-web03-zz1',       :domain => 'txel.systems', :ip => '172.16.210.73'               },
    :doc01  => {:host => 'au-dec-doc01-zz1',        :domain => 'txel.systems', :ip => '172.16.210.73'               },
  }

  nodes.each do |name, options|
    config.vm.define name do |node|
      node.vm.provision :hosts do |provisioner|
        provisioner.autoconfigure = false
        provisioner.add_host '172.16.210.10', ['tx-puppet01-zz1.txel.systems', 'tx-puppet01-zz1', 'puppet']
      end
      node.vm.provision :shell, :inline => "echo -e 'deb http://ftp.cl.debian.org/debian wheezy main non-free contrib\ndeb-src http://ftp.cl.debian.org/debian wheezy main non-free contrib\n\ndeb http://ftp.cl.debian.org/debian-security/ wheezy/updates main contrib non-free\ndeb-src http://ftp.cl.debian.org/debian-security/ wheezy/updates main contrib non-free' > /etc/apt/sources.list"
      node.vm.provision :shell, :inline => "apt-get update && apt-get -y dist-upgrade"
      node.vm.provision :shell, :inline => "sed -i '/templatedir/d' /etc/puppet/puppet.conf"
      node.vm.provision :shell, :inline => "sed -i 's/START=no/START=yes/g' /etc/default/puppet"
      node.vm.provision :shell, :inline => "echo -e '\n[agent]\npluginsync = true\nhttp_read_timeout = 5m\nhttp_connect_timeout = 5m\n' >> /etc/puppet/puppet.conf"
      node.vm.provision "puppet_server" do |puppet|
        puppet.puppet_server = "tx-puppet01-zz1.txel.systems"
        puppet.options = "--verbose"
      end
      node.vm.hostname = "#{options[:host]}.#{options[:domain]}"
      node.vm.network :private_network, ip: options[:ip]
      node.vm.provider :libvirt do |kvm|
        kvm.memory  = options[:mem] if options[:mem]
        kvm.cpus    = options[:cpu] if options[:cpu]
      end
    end
  end

end
