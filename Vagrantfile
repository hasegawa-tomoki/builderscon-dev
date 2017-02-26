# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/xenial64"
  config.vm.provision "shell", inline: <<-EOC
    sudo sed -i.bak -e "s%http://archive.ubuntu.com/ubuntu/%http://ftp.jaist.ac.jp/pub/Linux/ubuntu/%g" /etc/apt/sources.list
    sudo apt-get install -y python
  EOC

  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.manage_guest = true
  config.hostmanager.include_offline = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.aliases = %w(dev.builderscon.io)

  config.vm.define "buildersconio" do |node|
    node.vm.hostname = "buildersconio"

    #node.vm.network :public_network, ip: "10.2.0.216", netmask: "255.0.0.0", bridge: "en0: Ethernet" 
    node.vm.network "private_network", ip: "192.168.88.2"

    node.vm.synced_folder "www/", "/var/www"
    node.vm.synced_folder "builderscon/", "/var/go/src/github.com/builderscon"
    
    node.vm.provider :virtualbox do |vb|
      #vb.gui = true
      vb.name = 'buildersconio'
      vb.customize ['modifyvm', :id, '--memory', '2048', '--cpus', '2']
      vb.customize ['modifyvm', :id, '--natnet1', '192.168.101.0/24']
      vb.customize ['modifyvm', :id, '--natdnsproxy1', 'on']
      vb.customize ['modifyvm', :id, '--natdnshostresolver1', 'on']
    end
    
    node.vm.provision "ansible" do |ansible|
      ansible.groups = { "dev" => ["builderscon"] }
      ansible.playbook = "provisioning/dev.yml"
      #ansible.tags = [ "builderscon" ]
      ansible.raw_arguments = [
      #  "-v",
      #  "--diff",
      ]
    end
  end
end

