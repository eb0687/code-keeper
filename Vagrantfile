Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/jammy64"
  config.vm.hostname = "code-keeper-gitlab"
  config.vm.network "private_network", ip: "192.168.57.10"

  config.vm.provider "virtualbox" do |vb|
    vb.cpus = 2
    vb.memory = 6144
  end

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "ansible/playbooks/gitlab.yml"
    ansible.inventory_path = "ansible/inventories/vagrant/hosts.yml"
    ansible.vault_password_file = ".vault-password"
  end
end
