# Tinker_Board_2

## Tinker Board 2

sudo su
nano /etc/hostname    
nano /etc/hosts    
/etc/init.d/hostname.sh start    
reboot now    

sudo adduser aw    
sudo usermod -a -G sudo aw     

$dpkg-reconfigure keyboard-configuration
$service keyboard-setup restart 

apt install nano     

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
If you want to kill the above process: 
```
docker stop 7f8fa2e61190     
```
If you want to open a shell to the containter: 
```
docker exec -it 7f8fa2e61190 /bin/bash     
```

https://groups.io/g/openwebrx/topic/83519516#3531

/var/lib/docker/volumes/openwebrx-settings/_data

docker run --device /dev/bus/usb -p 8073:8073 -v openwebrx-settings:/var/lib/docker/volumes/openwebrx-settings/_data jketterl/openwebrx:stable &

docker ps
CONTAINER ID   IMAGE                       COMMAND   CREATED              STATUS              PORTS                                       NAMES     
04eb2220d76b   jketterl/openwebrx:stable   "/init"   About a minute ago   Up About a minute   0.0.0.0:8073->8073/tcp, :::8073->8073/tcp   great_albattani


docker stop 56be44f89d15

docker run -it jketterl/openwebrx-full admin adduser sm0hpl


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

