
Vagrant.configure("2") do |config|

   config.ssh.insert_key = false
 
  config.vm.define "dn0", primary: true do |dn0|
    dn0.vm.box = "bento/centos-7.2"
    dn0.vm.hostname = 'dn0'

    dn0.vm.network :private_network, ip: "192.168.56.101"
    dn0.vm.network :forwarded_port, guest: 22, host: 10122, id: "ssh"
    dn0.vm.network(:forwarded_port, {:guest=>9090, :host=>9090})     
    dn0.vm.network(:forwarded_port, {:guest=>8080, :host=>8080})     
    dn0.vm.network(:forwarded_port, {:guest=>9009, :host=>9191})
    dn0.vm.network(:forwarded_port, {:guest=>4848, :host=>4848})         

    dn0.vm.provision "file", source: "cluster.yml", destination: "cluster.yml"
    dn0.vm.provision "file", source: "~/.vagrant.d/insecure_private_key", destination: "~/.ssh/id_rsa"    
    dn0.vm.provision "shell", inline: "cp /home/vagrant/.ssh/authorized_keys /home/vagrant/.ssh/id_rsa.pub && sudo chown vagrant:vagrant /home/vagrant/.ssh/id_rsa.pub"
    
    dn0.vm.provider :virtualbox do |v|
      v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      v.customize ["modifyvm", :id, "--memory", 16048]
      v.customize ["modifyvm", :id, "--name", "dn0"]      
    end

    dn0.vm.provision :chef_solo do |chef|
        chef.cookbooks_path = "cookbooks"
        chef.json = {
          "karamel" => {
	    "default" =>      { 
              "private_ips" => ["192.168.56.101"]
	    },
          },
        }
        chef.add_recipe "karamel::install"
        chef.add_recipe "karamel::default"     
        chef.add_recipe "karamel::run"     
      end
    
  end

end

