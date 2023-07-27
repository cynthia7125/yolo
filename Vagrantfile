Vagrant.configure("2") do |config|
  config.vm.box = "geerlingguy/ubuntu2004"
  
  # Provisioning configuration for Ansible.
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "playbook.yml"
  end
end
