require 'yaml'

def load_customizations
  if !File.exist? "customizations"
    $stderr.puts "customizations not found"
    exit 1
  end
  YAML.load_file("customizations")
end
 
Vagrant.configure(2) do |config|
     # Setup virtual machine box. This VM configuration code is always executed.
    
    config.vm.define "pgsqlcentos6" do |pgsqlcentos6|
        pgsqlcentos6.vm.box = "hfm4/centos6"
        pgsqlcentos6.vm.network "private_network", ip: "192.168.32.9"
        pgsqlcentos6.vm.hostname = 'pgsqlcentos6'
        pgsqlcentos6.vm.provision "shell", inline:
          'echo "192.168.32.9 pgsqlcentos6" > /etc/hosts'
    end

    config.vm.define "pgsqlcentos7" do |pgsqlcentos7|
        pgsqlcentos7.vm.box = "hfm4/centos7"
        pgsqlcentos7.vm.network "private_network", ip: "192.168.32.10"
        pgsqlcentos7.vm.hostname = 'pgsqlcentos7'
        pgsqlcentos7.vm.provision "shell", inline:
          'echo "192.168.32.10 pgsqlcentos7" > /etc/hosts'

        pgsqlcentos7.vm.provision "ansible" do |ansible|
          ansible.playbook = "site.yml"
          ansible.verbose = 'v'
          ansible.extra_vars = load_customizations()
          ansible.host_key_checking = 'true'
          ansible.ask_sudo_pass = 'true'
          ansible.limit = 'all'
          ansible.groups = {
          "postgresql" => ["pgsqlcentos6", "pgsqlcentos7"]
          }
        end
    end

    config.vm.provider :virtualbox do |v|
      v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      v.customize ["modifyvm", :id, "--memory", 1024]
      v.customize ["modifyvm", :id, "--cpus", 1]
      v.customize ["modifyvm", :id, "--ioapic", "on"]
    end

    config.vm.provision "shell", inline:
      'sudo sed -ri "s/%admin ALL=NOPASSWD: ALL/%admin ALL=(ALL) NOPASSWD: ALL/" /etc/sudoers'
    
    config.vm.provision "shell", inline:
      'sudo yum -y update'

    config.ssh.forward_agent = true 

end
