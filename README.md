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

## firewalld

https://oitibs.com/easy-debian-10-server-firewall/     



apt install firewalld  
systemctl start firewalld     
firewall-cmd --permanent --zone=public --change-interface=eth0     
firewall-cmd --add-service="ssh" --permanent --zone=public     


systemctl list-units --type=service  [firewalld.service  loaded active  running  firewalld - dynamic firewall daemon]
systemctl status firewalld      
apt install firewall-config  
apt policy firewalld     see if it's running
https://computingforgeeks.com/how-to-install-and-configure-firewalld-on-debian/    


apt install nmap     
nmap -sTU -O IP-address-Here  

apt install ufw    
sudo nano /etc/default/ufw   (then make sure "IPV6" is set to "no". ip6 seems not to be in the kernel)    
https://www.digitalocean.com/community/tutorials/how-to-setup-a-firewall-with-ufw-on-an-ubuntu-and-debian-cloud-server

sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh    
sudo ufw allow 8073/tcp    
sudo ufw allow https    

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

