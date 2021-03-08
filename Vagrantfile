Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-20.04"
  
  config.vm.define "ubuntu_cicd" do |promdev|
      promdev.vm.provider "virtualbox" do |vbox|
          vbox.gui = false
          vbox.name = "ubuntu_cicd"
          vbox.memory = 2048
          vbox.cpus = 2
      end
      promdev.vm.hostname = "ubuntu-cicd"
      promdev.vm.network "private_network", ip: "192.168.50.21", netmask: "255.255.255.0"
  end
end