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

Create admin user for a container - see above:  docker exec -it 3b4417aeeda8 python3 /opt/openwebrx/openwebrx.py admin adduser admin  

Create /etc/modprobe.d/blacklist.conf and add this line:     
blacklist dvb_usb_rtl28xxu sdr_msi3101 msi001 msi2500 hackrf
run  update-initramfs -u

If you want to kill the above process:     
docker stop 7f8fa2e61190     

If you want to open a shell to the containter:     
docker exec -it 7f8fa2e61190 /bin/bash     

https://groups.io/g/openwebrx/topic/83519516#3531

/var/lib/docker/volumes/openwebrx-settings/_data

docker run --device /dev/bus/usb -p 8073:8073 -v openwebrx-settings:/var/lib/docker/volumes/openwebrx-settings/_data jketterl/openwebrx:stable &

docker ps
CONTAINER ID   IMAGE                       COMMAND   CREATED              STATUS              PORTS                                       NAMES     
04eb2220d76b   jketterl/openwebrx:stable   "/init"   About a minute ago   Up About a minute   0.0.0.0:8073->8073/tcp, :::8073->8073/tcp   great_albattani


docker stop 56be44f89d15

docker run -it jketterl/openwebrx-full admin adduser sm0hpl
