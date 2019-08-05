# -*- mode: ruby -*-
# vi: set ft=ruby :

MACHINES = {
  :master => {
    :box_name => "centos/7",
    :net => [
               {ip: '192.168.255.10', adapter: 2, netmask: "255.255.255.0", virtualbox__intnet: "router-net"},
            ]
  },
  :slave => {
    :box_name => "centos/7",
    :net => [
               {ip: '192.168.255.11', adapter: 2, netmask: "255.255.255.0", virtualbox__intnet: "router-net"},
            ]
  },
}

Vagrant.configure("2") do |config|

  MACHINES.each do |boxname, boxconfig|

    config.vm.define boxname do |box|

      box.vm.box = boxconfig[:box_name]
      box.vm.host_name = boxname.to_s

      boxconfig[:net].each do |ipconf|
        box.vm.network "private_network", ipconf
      end
      
      if boxconfig.key?(:public)
        box.vm.network "public_network", boxconfig[:public]
      end

      box.vm.provision "shell", inline: <<-SHELL
        mkdir -p ~root/.ssh
              cp ~vagrant/.ssh/auth* ~root/.ssh
        rpm -Uvh https://yum.postgresql.org/11/redhat/rhel-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm
        yum install -y postgresql11-server postgresql-libs python-psycopg2
        /usr/pgsql-11/bin/postgresql-11-setup initdb
        systemctl enable postgresql-11.service
      SHELL
      
      box.vm.provision "ansible" do |ansible|
        ansible.verbose = "vv"
        ansible.playbook = "playbook.yml"
      end
    end
  end  
end