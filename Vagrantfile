# -*- mode: ruby -*-
# vim: set ft=ruby :

MACHINES = {
  :master => {
        :box_name => "centos7",
        :ip_addr => '192.168.11.150'
  },
  :backup => {
        :box_name => "centos7",
        :ip_addr => '192.168.11.155'},
  :slave => {
        :box_name => "centos7",
        :ip_addr => '192.168.11.151'},
  :web => {
        :box_name => "centos7",
        :ip_addr => '192.168.11.153'}
}

Vagrant.configure("2") do |config|

  MACHINES.each do |boxname, boxconfig|

      config.vm.define boxname do |box|

          box.vm.box = boxconfig[:box_name]
          box.vm.host_name = boxname.to_s

          box.vm.network "private_network", ip: boxconfig[:ip_addr]

          box.vm.provider :virtualbox do |vb|
            vb.customize ["modifyvm", :id, "--memory", "1024"]
          end

      if boxname.to_s == "web"
      box.vm.provision "ansible" do |ansible|
      ansible.playbook = "ansible/main.yaml"
      ansible.host_key_checking = "false"
      ansible.limit = "all"
      end
      if boxname.to_s == "backup"
      box.vm.network "forwarded_port", guest: 8080, host: 8080
        end
	end
	end
  end
end
