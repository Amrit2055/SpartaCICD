Vagrant.configure("2") do |config|
  # VM1: Ubuntu (Web Server)
  config.vm.define "web_server" do |web|
    web.vm.box = "generic/ubuntu2004"
    web.vm.network "private_network", ip: "192.168.57.83"  # Updated subnet
    web.vm.provider "virtualbox" do |vb|
      vb.gui = true
      vb.memory = 900
    end
  end

  # VM2: Kali Linux (Database Server)
  config.vm.define "db_server" do |db|
    db.vm.box = "kalilinux/rolling"
    db.vm.network "private_network", ip: "192.168.57.81"  # Updated subnet
    db.vm.provider "virtualbox" do |vb|
      vb.gui = true
      vb.memory = 900
    end
  end
end

