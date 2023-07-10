# 42cursus-04-Born2BeRoot

<p align="center">
	<a href="#"><img src="https://game.42sp.org.br/static/assets/achievements/born2berootn.png"/></a>
</p>

<p align="center">
	<a href="https://github.com/JaeSeoKim/badge42"><img src="https://badge42.vercel.app/api/v2/clgz3vp5u001608l5gzuhclek/project/3145425" alt="evportel's 42 Born2beroot Score" /></a>
</p>

<p align="center">
	<strong>Part I - Instalação da VM</strong>
</p>

```bash
VM Machine
Linux -> Debian (64-bit)
Memory Size -> 1024 MB
Hard Disk -> Create a virtual hard disk now -> VDI (VirtualBox Disk Image)
Storage on physical hard disk -> Dynamically allocated
install
# select a language
English
# select your location
other -> South America -> Brazil
# configure locales
United States - en_US.UTF-8
# configure the keyboard
Brazilian
# configure the network - hostname
evportel42
# configure the network - domain
(empty)
# set up users and passwords - root password
123
# set up users and passwords - new user (name complete)
everton portela
# set up users and passwords - new user (user)
everton
# set up users and passwords - new user (password)
123
# configure the clock
São Paulo
# Partition disks
Guided - use entire disk and set up encrypted LVM -> SCDI2 (0,0,0) sda...
Separate /home partition -> Yes
#password encrypt disk
123 -> yes
# Amount of volume group to use for guided partitioning
max
# Conferir a tabela de partições e ... Finish
Finish partitioning and write changes to disk -> Yes
# Configure the package manager
No -> Brazil -> deb.debian.org -> (proxy info empty)
# Configuring popularity-contest
No
# Software selection
uncheck all items
# Configuring grub-pc
Yes -> /dev/sda (ata-VBOX...)
# Finish the installation
Continue
```

<p align="center">
	<strong>Part II - Comandos e Configurações</strong>
</p>

Checando a estrutura de partições

```bash
lsblk
```
Com o Debian rodando… **Primeiramente instalar o sudo**

```bash
su - 
apt-get update -y
apt-get upgrade -y
apt install sudo
usermod -aG sudo evportel
-- # getent group sudo para checar o grupo de usuário sudo
sudo visudo
-- # Encontre -> # User privilege specification
-- # digite your_username  	ALL=(ALL:ALL) ALL e salve o arquivo
evportel  	ALL=(ALL) ALL
```
**Retorne para seu usuário**

```bash
su evportel
cd /home/evportel
```
**Install Aptitude, Git, SSH and Vim**

```bash
sudo apt-get install aptitude -y
sudo apt-get install git -y
git --version # Para verificar a versão do Git

sudo apt install openssh-server -y
sudo systemctl status ssh
sudo apt-get install vim -y
sudo vim /etc/ssh/sshd_config
-- # Encontre esta linha '#Port22'
-- # Altere a linha para a porta 4242 sem o # (Hash) na frente dela
-- Port 4242
-- # salve e feche o arquivo: ESC -> :wq
sudo grep Port /etc/ssh/sshd_config # Para verificar se as configurações da porta estão corretas
sudo service ssh restart # Para reiniciar o serviço SSH
```
**Install UFW (Uncomplicated Firewall)**

```bash
sudo apt-get install ufw -y
sudo ufw enable # Para ativar o UFW
sudo ufw status numbered # Para verificar o status do UFW
sudo ufw allow ssh
sudo ufw allow 4242
sudo ufw status numbered # Para verificar o status do UFW, se a regra foi aplicada a porta 4242
```
**Conectando o SSH**

No `**VirtualBox**`, clique na sua máquina virtual e selecione `**settings**`
Clique em network, depois em Adapter 1, e altere para:  **`Attached to: Bridged Adapter`**
E altere `**Promiscuous Mode**` para `**Allow All**`

![Screenshot 01](/screencshot/vm_01.jpg)

**Voltando a Máquina Virtual**

```bash
sudo systemctl restart ssh 
-- # Para reiniciar o serviço ssh
sudo service sshd status
```
**Install Net-Tools**

```bash
sudo apt-get install net-tools

-- # Reinicie a máquina virtual
sudo reboot
-- # Pegue o IP da máquina virtual (inet 10.11.200.9) (netmask 255.255.0.0)
ifconfig
-- # e acesse user-name@ip-virtual-machine -p 4242
```
**Na sua máquina host ou em uma máquina na mesma rede**

```bash
-- # SSH user-name@ip-virtual-machine -p 4242
ssh everton@10.11.200.22 -p 4242
-- # Para encerrar sua conexão SSH
exit
```
**Definindo política de senha**

```bash
-- # Instala a biblioteca PAM
sudo apt-get install libpam-pwquality -y
-- # Editar algumas regras da política de senha
sudo vim /etc/pam.d/common-password
-- # Procure a linha **password requisite pam_pwquality.so retry=3**
-- # E complete essa linha com: 
-- # **minlen=10 ucredit=-1 dcredit=-1 maxrepeat=3 reject_username difok=7 enforce_for_root**
-- # Salve e feche o Vim

sudo vim /etc/login.defs 
-- # Procure pela linha **PASS_MAX_DAYS 9999** **PASS_MIN_DAYS 0** **PASS_WARN_AGE 7**
-- # E altere para **PASS_MAX_DAYS 30** **PASS_MIN_DAYS 2** **PASS_WARN_AGE 7**
-- # Salve e feche o Vim

sudo reboot
```