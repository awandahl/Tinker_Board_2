# Tinker_Board_2

## Tinker Board 2  needed initial changes

sudo su    
apt update    
apt upgrade    

dpkg-reconfigure keyboard-configuration     
service keyboard-setup restart    
reboot now

nano /etc/hostname    
nano /etc/hosts    
/etc/init.d/hostname.sh start    
reboot now    

passwd linaro

sudo adduser aw    
sudo usermod -a -G sudo aw     



apt install nano    

nano /etc/sysctl.conf      
net.ipv6.conf.all.disable_ipv6 = 1    
sysctl -p


systemctl -l status openwebrx   

The following commands are available:     

sudo systemctl enable openwebrx enables automatic startup of the receiver at boot     
sudo systemctl disable openwebrx disables it again    
sudo systemctl start openwebrx starts the service manually    
sudo systemctl stop openwebrx stops the service manually    
sudo systemctl restart openwebrx restarts the service. This is very useful after changing configuration.    

apt --fix-broken install      

apt upgrade libsndfile1   
sudo apt-get install git build-essential cmake libfftw3-dev python3 python3-setuptools rtl-sdr netcat libsndfile-dev librtlsdr-dev automake autoconf libtool pkg-config     

sudo apt-get install libsoapysdr0.6 libsoapysdr-dev soapysdr-tools
...also install sdr-specific modules:
sudo apt-get install soapysdr-module-all    

sudo apt-get install direwolf    

dpkg -i wsjtx_2.5.2_arm64.deb     
apt --fix-broken install     

sudo apt-get install sox    

## Logs and stuff

journalctl -f -n 0    


https://unix.stackexchange.com/questions/464979/restarting-service-based-on-log-output-keywords    

````
#!/bin/bash
regex="WARNING: SoapySDR::Device::readStream timeout!"
 
journalctl -f -n 0 |
while read line
do
    if [[ "$line" =~ $regexp ]]; then
        systemctl restart openwebrx
        echo I restarted the service at $(date) >> /home/aw/restart_log.txt
        /bin/sleep 30
    fi
done
````
## Resizing card

https://sourceforge.net/p/galileodebian/wiki/How%20to%20expand%20the%20root%20filesystem%20to%20use%20the%20entire%20SD%20card/

## GPIO

Examples:     

https://randomnerdtutorials.com/raspberry-pi-web-server-using-flask-to-control-gpios/     
https://www.hackster.io/adhyoksh/controlling-gpio-pins-of-raspberry-pi-with-web-page-2d5bdc     
https://www.foobarflies.io/python-on-the-pi-how-to-run-a-flask-gpio-web-service/     


About TinkerBoard 2 and GPIOs:     

https://tinker-board.asus.com/forum/index.php?/topic/14984-gpio/&tab=comments#comment-15331    

![image](https://user-images.githubusercontent.com/62021989/148692445-b2ee1f15-ae22-45d3-9554-8151b05318fb.png)

````
output from 'gpio readall'
 gpio readall
 +-----+-----+---------+------+---+--Tinker--+---+------+---------+-----+-----+
 | CPU | wPi |   Name  | Mode | V | Physical | V | Mode | Name    | wPi | CPU |
 +-----+-----+---------+------+---+----++----+---+------+---------+-----+-----+
 |     |     |    3.3v |      |   |  1 || 2  |   |      | 5v      |     |     |
 |  73 |   8 | GPIO2B1 |   IN | 1 |  3 || 4  |   |      | 5v      |     |     |
 |  74 |   9 | GPIO2B2 |   IN | 1 |  5 || 6  |   |      | 0v      |     |     |
 |   8 |   7 | CLKOUT2 |  CLK | 1 |  7 || 8  | 1 | IN   | GPIO2C1 | 15  | 81  |
 |     |     |      0v |      |   |  9 || 10 | 1 | IN   | GPIO2C0 | 16  | 80  |
 |  83 |   0 | GPIO2C3 |   IN | 1 | 11 || 12 | 0 | IN   | GPIO3D0 | 1   | 120 |
 |  85 |   2 | GPIO2C5 |   IN | 0 | 13 || 14 |   |      | 0v      |     |     |
 |  84 |   3 | GPIO2C4 |   IN | 0 | 15 || 16 | 0 | IN   | GPIO2C6 | 4   | 86  |
 |     |     |    3.3v |      |   | 17 || 18 | 1 | IN   | GPIO2C7 | 5   | 87  |
 |  40 |  12 | GPIO1B0 |   IN | 1 | 19 || 20 |   |      | 0v      |     |     |
 |  39 |  13 | GPIO1A7 |   IN | 1 | 21 || 22 | 0 | IN   | GPIO3D4 | 6   | 124 |
 |  41 |  14 | GPIO1B1 |   IN | 1 | 23 || 24 | 1 | IN   | GPIO1B2 | 10  | 42  |
 |     |     |      0v |      |   | 25 || 26 | 0 | IN   | GPIO0A6 | 11  | 6   |
 |  71 |  30 | GPIO2A7 |   IN | 1 | 27 || 28 | 1 | IN   | GPIO2B0 | 31  | 72  |
 | 126 |  21 | GPIO3D6 |   IN | 0 | 29 || 30 |   |      | 0v      |     |     |
 | 125 |  22 | GPIO3D5 |   IN | 0 | 31 || 32 | 0 | IN   | GPIO4C2 | 26  | 146 |
 | 150 |  23 | GPIO4C6 |  OUT | 1 | 33 || 34 |   |      | 0v      |     |     |
 | 121 |  24 | GPIO3D1 |   IN | 0 | 35 || 36 | 1 | IN   | GPIO2C2 | 27  | 82  |
 | 149 |  25 | GPIO4C5 |   IN | 0 | 37 || 38 | 0 | IN   | GPIO3D3 | 28  | 123 |
 |     |     |      0v |      |   | 39 || 40 | 0 | IN   | GPIO3D7 | 29  | 127 |
 +-----+-----+---------+------+---+----++----+---+------+---------+-----+-----+
 | CPU | wPi |   Name  | Mode | V | Physical | V | Mode | Name    | wPi | CPU |
 +-----+-----+---------+------+---+--Tinker--+---+------+---------+-----+-----+

````

## iptables Debian 9 Stretch

https://upcloud.com/community/tutorials/configure-iptables-debian/

Inbound traffic:

````
sudo iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT     
sudo iptables -A INPUT -p tcp --dport ssh -j ACCEPT    
sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT    
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT    

# Default rule = DROP:    
sudo iptables -P INPUT DROP   

mkdir /etc/iptables    
sudo iptables-save > /etc/iptables/rules.v4

# Overwrite the current rules    
sudo iptables-restore < /etc/iptables/rules.v4    
# Add the new rules keeping the current ones    
sudo iptables-restore -n < /etc/iptables/rules.v4    

sudo apt install iptables-persistent    
````

After the installation the initial setup will ask to save the current rules for IPv4 and IPv6, just select Yes and press enter for both.    
If you make further changes to your iptables rules, remember to save them again using the same command as above. The iptables-persistent looks for the files     rules.v4 and rules.v6 under /etc/iptables.    

Iptables isn’t commonly a service by itself, rather just a command-line tool for controlling the kernel firewall. You can effectively disable it by allowing all traffic and flushing the rules.    

````
iptables -P INPUT ACCEPT    
iptables -P OUTPUT ACCEPT    
iptables -P FORWARD ACCEPT    
iptables -F    
````
````
# Generated by iptables-save v1.6.0 on Sun Dec 26 21:14:58 2021
*nat
:PREROUTING ACCEPT [0:0]
:INPUT ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]
:POSTROUTING ACCEPT [0:0]
COMMIT
# Completed on Sun Dec 26 21:14:58 2021
# Generated by iptables-save v1.6.0 on Sun Dec 26 21:14:58 2021
*filter
:INPUT DROP [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]
-A INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
-A INPUT -p tcp -m tcp --dport 22 -j ACCEPT
-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT
COMMIT
# Completed on Sun Dec 26 21:14:58 2021
````
-----

## Let's encrypt

apt install libffi-dev     

https://certbot.eff.org/instructions?ws=nginx&os=pip     

````
sudo apt update
sudo apt install python3 python3-venv libaugeas0
    
sudo python3 -m venv /opt/certbot/
sudo /opt/certbot/bin/pip install --upgrade pip
sudo /opt/certbot/bin/pip install certbot certbot-nginx
sudo ln -s /opt/certbot/bin/certbot /usr/bin/certbot
sudo certbot --nginx
    
echo "0 0,12 * * * root /opt/certbot/bin/python -c 'import random; import time; time.sleep(random.random() * 3600)' && sudo renew -q" | sudo tee -a /etc/crontab > /dev/null

````
#### TIMESHIFT

sudo timeshift --create --comments "new backup" --tags D --snapshot-device /dev/sda1    

Saving to device: /dev/sda1, mounted at path: /run/timeshift/backup     
Path to daily backups: /run/timeshift/backup/timeshift/snapshots-daily    

sudo timeshift --restore --snapshot '2019-01-12_16-29-08'

udisksctl unmount -b /dev/sda1

-----

#### NGINX

sudo systemctl restart nginx   
sudo nginx -t    
systemctl status nginx

-----

https://oitibs.com/easy-debian-10-server-firewall/     


apt install --reinstall iptables
apt install firewalld  
systemctl start firewalld   
systemctl enable firewalld     
firewall-cmd --permanent --zone=public --change-interface=eth0     
firewall-cmd --add-service="ssh" --permanent --zone=public     
firewall-cmd --add-service="https" --permanent --zone=public   
....
firewall-cmd --reload     


systemctl list-units --type=service  [firewalld.service  loaded active  running  firewalld - dynamic firewall daemon]
systemctl status firewalld      
apt install firewall-config  
apt policy firewalld     see if it's running
https://computingforgeeks.com/how-to-install-and-configure-firewalld-on-debian/    


apt install nmap     
nmap -sTU -O IP-address-Here  

## ufw Debian 10 Buster

apt install ufw    
sudo nano /etc/default/ufw   (then make sure "IPV6" is set to "no". ip6 seems not to be in the kernel)    
https://www.digitalocean.com/community/tutorials/how-to-setup-a-firewall-with-ufw-on-an-ubuntu-and-debian-cloud-server

````
sudo ufw default deny incoming     
sudo ufw default allow outgoing     
#sudo ufw allow ssh      
sudo ufw allow 8073/tcp       
sudo ufw allow https       
sudo ufw allow from 15.15.15.15 to any port 22 proto tcp    
sudo ufw allow from 15.15.15.15 to any port 22 proto tcp    
...
````





sudo systemctl enable ufw

This command for starting ufw at boot doesn't work: sudo systemctl enable ufw     
so edit like below:       

nano /etc/rc.local (edit last in the file) 
````
# start ufw    
ufw enable    
exit 0    
````




## Take down wlp1s0    
nano /etc/network/interfaces    
````
# interfaces(5) file used by ifup(8) and ifdown(8)    
# Include files from /etc/network/interfaces.d:    
source-directory /etc/network/interfaces.d    
iface wlp1s0 inet manual    
````
sudo service network-manager restart   

## Docker    

Docker on Debian Stretch: https://tecadmin.net/install-docker-on-debian-9-stretch/ 

```
sudo apt-get purge docker lxc-docker docker-engine docker.io    
sudo apt-get install apt-transport-https ca-certificates curl gnupg2 software-properties-common    
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -    
sudo add-apt-repository "deb [arch=arm64] https://download.docker.com/linux/debian stretch stable"    
sudo apt-get update    
sudo apt-get install docker-ce    
sudo systemctl status docker    
```

OWRX Docker setup: https://github.com/jketterl/openwebrx/wiki/Getting-Started-using-Docker     
OWRX Docker:  https://hub.docker.com/u/jketterl    

Update docker image:  https://github-wiki-see.page/m/jketterl/openwebrx/wiki/How-to-update-openwebrx     


## OpenWebRX

Install as root:  

```
docker volume create openwebrx-settings     

docker run --tmpfs=/tmp/openwebrx --restart=unless-stopped --device /dev/bus/usb -p 8073:8073 -v openwebrx-settings:/var/lib/openwebrx jketterl/openwebrx:stable &    
docker run --tmpfs=/tmp/openwebrx --restart=unless-stopped --device /dev/bus/usb -p 8073:8073 -v openwebrx-settings:/var/lib/openwebrx jketterl/openwebrx-full:stable &    
docker run --tmpfs=/tmp/openwebrx --restart=unless-stopped --device /dev/bus/usb -p 8073:8073 -v openwebrx-settings:/var/lib/openwebrx jketterl/openwebrx-full:latest &    
```
docker ps   
```
CONTAINER ID   IMAGE                       COMMAND   CREATED         STATUS         PORTS                                       NAMES     
7f8fa2e61190   jketterl/openwebrx:stable   "/init"   3 minutes ago   Up 3 minutes   0.0.0.0:8073->8073/tcp, :::8073->8073/tcp   festive_noyce   
```

Create admin user for a container - see above:  
```
docker exec -it 3b4417aeeda8 python3 /opt/openwebrx/openwebrx.py admin adduser admin  
```
Blacklist kernel modules: create /etc/modprobe.d/blacklist.conf and add this line:

```
nano /etc/modprobe.d/blacklist.conf
blacklist dvb_usb_rtl28xxu sdr_msi3101 msi001 msi2500 hackrf
update-initramfs -u
```
Start docker at boot:
```
sudo systemctl enable docker
```
If you want to kill/restart/read logs from the above process: 
```
docker stop f5d3b67ca250  
docker restart f5d3b67ca250     
docker container logs --tail 200 f5d3b67ca250    
```
If you want to open a shell to the containter: 
```
docker exec -it f680e11cbf97 /bin/bash     
```

https://groups.io/g/openwebrx/topic/83519516#3531

/var/lib/docker/volumes/openwebrx-settings/_data

docker run --device /dev/bus/usb -p 8073:8073 -v openwebrx-settings:/var/lib/docker/volumes/openwebrx-settings/_data jketterl/openwebrx:stable &

docker ps
CONTAINER ID   IMAGE                       COMMAND   CREATED              STATUS              PORTS                                       NAMES     
04eb2220d76b   jketterl/openwebrx:stable   "/init"   About a minute ago   Up About a minute   0.0.0.0:8073->8073/tcp, :::8073->8073/tcp   great_albattani


docker stop 56be44f89d15

docker run -it jketterl/openwebrx-full admin adduser sm0hpl

## HackRF

SoapySDR:   

sudo apt-get install cmake g++ libpython-dev python-numpy swig

git clone https://github.com/pothosware/SoapySDR.git
cd SoapySDR

mkdir build
cd build
cmake ..
make -j4
sudo make install
sudo ldconfig #needed on debian systems
SoapySDRUtil --info

SoapyHackRF

apt install hackrf    
apt install libhackrf-dev

git clone https://github.com/pothosware/SoapyHackRF.git
cd SoapyHackRF
mkdir build
cd build
cmake ..
make
sudo make install

SoapySDRUtil --probe="driver=hackrf"

## SoapyRemote




![image](https://user-images.githubusercontent.com/62021989/146646530-b11c5dbd-766a-4210-952e-726c92127e02.png)



## VNC and other OrangePi stuff...

In the ‘Terminal’ window make the updates:    
apt-get update    
apt-get upgrade    

Install TightVNC server to have remote graphical access via the VNC client extension of the Chrome browser or TightVNC for Windows.    

apt-get install tightvncserver    

Install xfonts-base to avoid the “could not open default font” fixed “” error when launching vncserver.    

apt-get install xfonts-base    

Switch to a user account and not root:    

su username    

Start VNC in manual mode:    

vncserver :1    

You will be asked for a password. Do not put more than 8 characters. You can now test VNC with for example the chrome VNC extension by connecting to ‘orangepi_ip_address: 5901’.    

For an automated launch we will create a script file:    

sudo nano /usr/local/bin/mon_vnc.sh

```
#!/bin/bash
PATH="$PATH:/usr/bin/"
DISPLAY="1"
DEPTH="16"
GEOMETRY="1920x1080"
OPTIONS="-depth ${DEPTH} -geometry ${GEOMETRY} :${DISPLAY}"
case "$1" in
start)
/usr/bin/vncserver ${OPTIONS}
;;
stop)
/usr/bin/vncserver -kill :${DISPLAY}
;;
restart)
$0 stop
$0 start
;;
esac
exit 0
```

Copy the text above and copy it into nano with a right click if you are using Bitwise SSH. Remember to make this file executable.    

sudo chmod +x /usr/local/bin/mon_vnc.sh    

We can now perform the following 3 operations:    

/usr/local/bin/mon_vnc.sh start    
/usr/local/bin/mon_vnc.sh stop    
/usr/local/bin/mon_vnc.sh restart    

For an automatic launch of VNC when booting the orange pi we create a service in ‘systemd’.    

sudo nano /lib/systemd/system/mon_vnc.service    

```
[Unit]
 Description=Manage Mon Serveur VNC 
 [Service]
 Type=forking
 ExecStart=/usr/local/bin/mon_vnc.sh start
 ExecStop=/usr/local/bin/mon_vnc.sh stop
 ExecReload=/usr/local/bin/mon_vnc.sh restart
 User=username
 [Install]
 WantedBy=multi-user.target
```

Replace username with the user. For the inclusion of this new service in systemd.    
systemctl daemon-reload    
systemctl enable mon_vnc.service    

Now you can reboot your system and verify with VNC the connection in graphical mode.    

systemctl start mon_vnc.service    
systemctl stop mon_vnc.service    
systemctl restart mon_vnc.service    
systemctl status mon_vnc.service    

