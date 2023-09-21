Eric Ferreira Gomes 
20/09/2023

Vagrant 

Requisitos:

Vagrant;
VirtualBox
Sistema operacional Linux.
VisualStudio Code ou Abridor de arquivo de Texto.

Instalação:

Para iniciar a com a utilização do Vagrant, você deve primeiramente baixar a ferramenta, para isso, abra o terminal (Ctrl+Alt+t), e digite o seguinte comando: sudo apt install vagrant , assim baixará a ferramenta. 
	O vagrant utiliza-se o VirtualBox, para criação das máquinas virtuais, para download do  VirtualBox, utilize o seguinte comando em seu terminal: sudo apt install virtualbox-qt, assim a ferramenta será instalada em seu computador.

Inicializando o Vagrant

Para iniciar o Vagrant , você deve utilizar uma imagem de um sistema operacional, assim você poderá qualquer sistema, no site site oficial: https://app.vagrantup.com/boxes/search , porém, para a criação das três máquinas, será utilizado o ubuntu server.
	Primeiramente, abre o terminal (Ctrl+Alt+t), e em seguida crie uma pasta com o nome que você deseja, use o comando: mkdir <NomeDaPasta>, em seguida entre na pasta com o comando cd  <NomeDaPastas>, em sequência, use o comando: vagrant init gusztavvargadr/ubuntu-server, com isso será criado um arquivo com o nome Vagrantfile.

 Requisitos das VM

Vm1: 	
Sistema Operacional: Ubuntu Server;
Interface de Rede 1 (eth0): IP Privado Estático (192.168.56.10); 
Função:  Servidor Web (Instalar o Apache);
Pasta compartilhada do host /var/www/html.

Vm2:
Sistema Operacional: Ubuntu Server;
Interface de Rede 1 (eth0): IP Privado Estático (192.168.56.11);
Função:  Servidor de Banco de dados (Instalar Mysql ou PostgreSQL).

Vm3: 
Sistema Operacional: Ubuntu Server;
Interface de Rede 1 (eth0): IP Privado Estático (192.168.56.12);
Interface de Rede 2 (eth1): IP Público DHCP;
Função Gateway de rede;
Deve fornecer acesso a internet para as Vms: Vm1 e Vm2.


Criação das VMs:
	
	Abra o arquivo Vagrantfile, no VisualStudio Code ou em um editor de texto e assim iremos inicializar as criações das máquinas virtuais
Primeiramente utilize o comando : Vagrant.configure("2") do |config| config.vm.box = "gusztavvargadr/ubuntu-server" ,para criar assim criar todas 3 Vms com o mesmo sistema Operacional, com a mesma imagem. Para criar a primeira maquinas virtual comece com config.vm.define "vm1" do |vm1|, ou seja, você deu um apelido para a primeira máquina, e através do apelido que você conseguirá configurar a máquina. Em seguida, criaremos o IP privado, para isso utilize o comando: vm1.vm.network "private_network", ip: "192.168.56.10", assim definimos o ip dessa máquina, pode ser criado também uma porta de acesso utilize o seguinte comando vm1.vm.network "forwarded_port", guest: 80, host: 8000. Devemos criar uma pasta compartilhada de seu computador para o sua máquina virtual, para isso utilize o comando vm1.vm.synced_folder "/var/www/html", "/var/www/html", e por fim para a instalação do apache utilize e vincular ao gateway de outra vm3 o comando:   vm1.vm.provision "shell", inline: <<-SHELL
run: "always",
inline: "ip route del default via 192.168.56.12 || true"

  apt-get update
  apt-get install -y apache2
  
  SHELL

end

 Obs: Cada bloco de código começa com o do e precisa ser fechado com o end

Para a criação da Vm2 utilize o comando  config.vm.define "vm2" do |vm2| , para criar e definir a máquina virtual dois, para definir o ip da rede privado, utilize o comando: vm2.vm.network "private_network", ip: "192.168.56.11". E para baixar o mysql  e conectar o gateway na vm3 utilize o comando: vm2.vm.provision "shell", inline: <<-SHELL
run: "always",
inline: "ip route del default via 192.168.56.12 || true"

apt-get update
apt-get install -y mysql-server

SHELL.
Por fim a criação da Vm3 inicie igual os anteriores com config.vm.define "vm3" do |vm3| , para definir a 3ª VM, para definir um IP Privada usa-se o comando vm3.vm.network "private_network", ip: "192.168.56.12", depois faça o comando vm3.vm.network "public_network", bridge: "enp4s0", para a criação da Bridge para conexão entre máquinas.  Utilize o vm3.vm.hostname = "vm3", para definir como host a vm3, para o comando vm3.vm.provision "shell", inline: <<-SHELL
sudo iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE
sudo ip route add 192.168.56.10 via 192.168.56.12
sudo ip route add 192.168.56.11 via 192.168.56.12

export DEBIAN_FRONTEND=noninteractive
echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward
SHELL

Esse comando acima,  é utilizado para criar um nat na primeira linha, e para vincular o gateway nas máquinas, sendo o código da linha 2 e 3 correção de instalação de pacote, para compartilhamento de rede.

Resultado

Se tudo estiver nos conformes, as VMs será criadas e estará funcionando, quando iniciar a máquina você utilize o login vagrant e a senha vagrant para logar, e você pode usar apache –version, na máquina 2, mysql -v para ver a versão do mysql, você pode também pode colocar ping é número de protocolo e ver quantos de ping para ver o ms de cada ip, na VM1 pode dar um cd .., cd .. até sair da pasta home, assim você segue com cd /var/www/html, e assim usar o comando e da wget http://192.168.56.10 e assim você verá o site.



	
