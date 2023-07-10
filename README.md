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
usermod -aG sudo everton 
-- # getent group sudo para checar o grupo de usuário sudo
sudo visudo
-- # Encontre -> # User privilege specification
-- # digite your_username  	ALL=(ALL:ALL) ALL e salve o arquivo
everton  	ALL=(ALL) ALL
```
**Retorne para seu usuário**

```bash
su everton
cd /home/everton
```