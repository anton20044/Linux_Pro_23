Vagrant.configure("2") do |config|
config.vm.box = "ubuntu/jammy64" 
config.vm.define "server" do |server| 
server.vm.hostname = "server" 
server.vm.network "private_network", ip: "192.168.57.20" 
      server.vm.provision "shell", inline: <<-SHELL
        mkdir -p ~root/.ssh
        cp ~vagrant/.ssh/auth* ~root/.ssh
        sed -i 's/^PasswordAuthentication.*$/PasswordAuthentication yes/' /etc/ssh/sshd_config.d/60-cloudimg-settings.conf
         sed -i 's/^PermitRootLogin.*$/PermitRootLogin yes/' /etc/ssh/sshd_config
        systemctl restart sshd.service
      SHELL

end 

config.vm.define "client" do |client| 
client.vm.hostname = "client.loc" 
client.vm.network "private_network", ip: "192.168.57.30" 
      client.vm.provision "shell", inline: <<-SHELL
        mkdir -p ~root/.ssh
        cp ~vagrant/.ssh/auth* ~root/.ssh
        sed -i 's/^PasswordAuthentication.*$/PasswordAuthentication yes/' /etc/ssh/sshd_config.d/60-cloudimg-settings.conf
        systemctl restart sshd.service
      SHELL

client.vm.provision "ansible" do |ansible|
    ansible.playbook = "ansible/provision.yml"
    ansible.inventory_path = "ansible/hosts"
    ansible.host_key_checking = "false"
    ansible.limit = "all"
  end


end 
end
