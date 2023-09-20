Vagrant.configure("2") do |config|
  config.vm.box = "gusztavvargadr/ubuntu-server"

config.vm.define "vm1" do |vm1|
  vm1.vm.network "forwarded_port", guest: 80, host: 8000
  vm1.vm.network "private_network", ip: "192.168.56.10"
  vm1.vm.synced_folder "/var/www/html", "/var/www/html"

  vm1.vm.provision "shell", inline: <<-SHELL
  apt-get update
  apt-get install -y apache2
  
  SHELL

end
  
config.vm.define "vm2" do |vm2|
  vm2.vm.network "forwarded_port", guest: 80, host: 8888 
  vm2.vm.network "private_network", ip: "192.168.56.11"

  vm2.vm.provision "shell", inline: <<-SHELL
  apt-get update
  apt-get install -y mysql-server
  SHELL

end
config.vm.define "vm3" do |vm3|
  vm3.vm.network "private_network", ip: "192.168.56.12"
  vm3.vm.network "public_network", bridge: "en1: Wi-Fi (AirPort)"
  vm3.vm.hostname = "vm3"
  vm3.vm.provision "shell", inline: <<-SHELL
        export DEBIAN_FRONTEND=noninteractive
        sudo ip link set eth0 up
        echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward
      SHELL
    

end
end 
 # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
