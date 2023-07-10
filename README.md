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
**Criando grupos de usuário**

```bash
sudo groupadd user42
sudo groupadd evaluation
-- # Para verificar se os grupos foram criados
getent group
```
**Atribuindo usuários a grupos**

```bash
-- # Para verificar todos os usuários locais
cut -d: -f1 /etc/passwd

-- # Adicionar usuário 'evportel' ao grupo 'user42'
sudo usermod -aG user42 evportel

-- # Adicionar usuário ao grupo evaluation
sudo usermod -aG evaluation evportel

-- # Para verificar se o usuário está mesmo no grupo aplicado
getent group user42
getent group evaluation
groups
```
**Criando sudo.log**

```bash
cd /var/log/
-- # Caso não exista, crie a pasta sudo
sudo mkdir sudo
cd sudo
sudo touch sudo.log
```
**Configurando o grupo sudoers**

```bash
sudo nano /etc/sudoers
-- # Edite o arquivo com adicionando as seguintes configurações
Defaults	env_reset
Defaults	mail_badpass
Defaults	secure_path="/usr/local/sbin:/usr/local/bin:/usr/bin:/sbin:/bin"
Defaults	badpass_message="Password is wrong, please try again!"
Defaults	passwd_tries=3
Defaults	logfile="/var/log/sudo/sudo.log"
Defaults	log_input, log_output
Defaults	requiretty
```
**Configurando crontab**
Como as ferramentas de rede já estão instaladas, basta apenas criar os arquivos de configuração.

```bash
cd /usr/local/bin/
sudo touch monitoring.sh
sudo chmod 777 monitoring.sh
nano monitoring.sh
```
```bash
#!/bin/bash
arc=$(uname -a)
pcpu=$(grep "physical id" /proc/cpuinfo | sort | uniq | wc -l) 
vcpu=$(grep "^processor" /proc/cpuinfo | wc -l)
fram=$(free -m | awk '$1 == "Mem:" {print $2}')
uram=$(free -m | awk '$1 == "Mem:" {print $3}')
pram=$(free | awk '$1 == "Mem:" {printf("%.2f"), $3/$2*100}')
fdisk=$(df -BG | grep '^/dev/' | grep -v '/boot$' | awk '{ft += $2} END {print ft}')
udisk=$(df -BM | grep '^/dev/' | grep -v '/boot$' | awk '{ut += $3} END {print ut}')
pdisk=$(df -BM | grep '^/dev/' | grep -v '/boot$' | awk '{ut += $3} {ft+= $2} END {printf("%d"), ut/ft*100}')
cpul=$(top -bn1 | grep '^%Cpu' | cut -c 9- | xargs | awk '{printf("%.1f%%"), $1 + $3}')
lb=$(who -b | awk '$1 == "system" {print $3 " " $4}')
lvmu=$(if [ $(lsblk | grep "lvm" | wc -l) -eq 0 ]; then echo no; else echo yes; fi)
ctcp=$(ss -neopt state established | wc -l)
ulog=$(users | wc -w)
ip=$(hostname -I)
mac=$(ip link show | grep "ether" | awk '{print $2}')
cmds=$(journalctl _COMM=sudo | grep COMMAND | wc -l)
wall "	#Architecture: $arc
	#CPU physical: $pcpu
	#vCPU: $vcpu
	#Memory Usage: $uram/${fram}MB ($pram%)
	#Disk Usage: $udisk/${fdisk}Gb ($pdisk%)
	#CPU load: $cpul
	#Last boot: $lb
	#LVM use: $lvmu
	#Connections TCP: $ctcp ESTABLISHED
	#User log: $ulog
	#Network: IP $ip ($mac)
	#Sudo: $cmds cmd"
```

<aside>
	<em>
🤓 Você até pode abrir um terminal (na sua máquina host) via acesso SSH que já está funcionando e fazer o tradicional: Ctrl+c Ctrl+v
Mas recomendo escrever e compreender cada comando \_(°-°)_/
	</em>
</aside>

```bash
**sudo visudo**
#-- Procure a linha ‘# Allow members of group sudo to execute any command'
#-- username ALL=(root) NOPASSWD: /usr/local/bin/monitoring.sh 
#-- Ficará assim:
**# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL
everton ALL=(root) NOPASSWD: /usr/local/bin/monitoring.sh**
```

```coq
sudo reboot
sudo /usr/local/bin/monitoring # Para executar o script como su (SuperUsuário)
sudo crontab -u root -e # Para abrir o crontab
*/10 * * * *        /usr/local/bin/monitoring.sh #(insira isso no final do arquivo)
```
*End Mandatory*