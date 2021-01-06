# Windows
* Chocolatey
* Chrome
* 7+ Taskbar Tweaker
  * https://tweaker.rammichael.com/
* Visual Studio
* SSMS


# RPi
sudo apt update && sudo apt upgrade -y && sudo apt autoremove && sudo apt autoclean  
sudo apt install usbmount -y  
sudo nano /etc/ssh/sshd_config  
change port  
sudo apt install apt-transport-https dirmngr gnupg ca-certificates  
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF  
echo "deb https://download.mono-project.com/repo/debian stable-raspbianbuster main" | sudo tee /etc/apt/sources.list.d/mono-official-stable.list  
sudo apt update  
sudo apt install mono-complete -y

### UnRAR
sudo apt-get remove unrar-free & sudo apt autoremove & sudo apt autoclean  
sudo nano /etc/apt/sources.list  
uncomment 'deb-src http://raspbian.raspberrypi.org/raspbian/ buster main contrib non-free rpi'  
cd /tmp  
sudo apt update  
sudo apt-get build-dep unrar-nonfree  
sudo apt-get source -b unrar-nonfree  
sudo dpkg -i unrar*.deb


### Mount USB Drive
sudo apt-get install ntfs-3g  
sudo mkdir /media/usb  
sudo chmod 770 /media/usb  
sudo cp /etc/fstab /etc/fstab.backup  
sudo nano /etc/fstab  
`/dev/sda1 /media/usb ntfs uid=pi,gid=pi 0 0`

### Samba
sudo apt install samba samba-common-bin -y  
sudo nano /etc/samba/smb.conf  
`[usb]  
   path = /media/usb  
   writeable=Yes  
   create mask=0777  
   directory mask=0777  
   public=no`  
sudo smbpasswd -a pi  
sudo usermod -a -G sambashare pi  
sudo usermod -a -G root pi  
sudo systemctl restart smbd

### Transmission
sudo apt install transmission-daemon -y  
sudo systemctl stop transmission-daemon  
mkdir -p /media/usb/torrent-inprogress  
mkdir -p /media/usb/torrent-complete  
sudo chown -R pi:pi /media/usb/torrent-inprogress  
sudo chown -R pi:pi /media/usb/torrent-complete  
sudo nano /etc/transmission-daemon/settings.json  
`"incomplete-dir": "/media/usb/torrent-inprogress",  
"download-dir": "/media/usb/torrent_complete",  
"incomplete-dir-enabled": true,  
"rpc-password": "Your_Password",  
"rpc-username": "Your_Username",  
"rpc-whitelist": "192.168.*.*",`  
sudo nano /etc/init.d/transmission-daemon  
`USER=pi`  
sudo nano /etc/systemd/system/multi-user.target.wants/transmission-daemon.service  
`user=pi`  
sudo systemctl daemon-reload  
sudo chown -R pi:pi /etc/transmission-daemon  
sudo mkdir -p /home/pi/.config/transmission-daemon/  
sudo ln -s /etc/transmission-daemon/settings.json /home/pi/.config/transmission-daemon/  
sudo chown -R pi:pi /home/pi/.config/transmission-daemon/  
sudo systemctl start transmission-daemon

### NZBGET
sudo apt install nzbget 7zip-full -y  
sudo nano /etc/systemd/system/nzbget.service  
`
[Unit]
Description=NZBGet Binary News File Grabber
After=network.target

[Service]
Type=forking
User=pi
Group=pi
ExecStart=/usr/bin/nzbget --daemon --configfile /etc/nzbget.conf
ExecReload=/usr/bin/nzbget --reload
ExecStop=/usr/bin/nzbget --quit
Restart=always
SyslogIdentifier=NZBGet

[Install]
WantedBy=multi-user.target
`
sudo systemctl daemon-reload  
sudo systemctl enable nzbget 
sudo nano /etc/nzbget.conf
`
MainDir=/media/usb/nzbget  
ControlIP=0.0.0.0
`
sudo chown pi:pi /etc/nzbget.conf
sudo systemctl start nzbget  
change settings->unpack->

### Sonarr
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 0xA236C58F409091A18ACA53CBEBFF6B99D9B78493  
echo "deb http://apt.sonarr.tv/ master main" | sudo tee /etc/apt/sources.list.d/sonarr.list  
sudo apt update  
sudo apt install nzbdrone -y  
sudo chown -R pi:pi /opt/NzbDrone
sudo nano /etc/systemd/system/sonarr.service  
'
[Unit]
Description=Sonarr Daemon
After=network.target
 
[Service]
User=pi
Group=pi
 
Type=simple

ExecStart=/usr/bin/mono --debug /opt/NzbDrone/NzbDrone.exe -nobrowser -data=/opt/config/NzbDrone
TimeoutStopSec=20
KillMode=process
Restart=on-failure
RestartSec=5
 
[Install]
WantedBy=multi-user.target
'
sudo systemctl daemon-reload  
sudo systemctl enable sonarr  
sudo systemctl start sonarr  

### Radarr

curl -L  "https://radarr.servarr.com/v1/update/master/updatefile?os=linux&runtime=netcore&arch=arm" -o /tmp/Radarr.tgz --progress-bar  
sudo tar xvzf /tmp/Radarr.tgz -C /opt/
sudo rm -rf /tmp/Radarr.tgz  
sudo chown -R pi:pi /opt/Radarr  
sudo nano /etc/systemd/system/radarr.service  
'
[Unit]
Description=Radarr Daemon
After=network.target
 
[Service]
User=pi
Group=pi
 
Type=simple

ExecStart=/opt/Radarr/Radarr -nobrowser -data=/opt/config/Radarr
TimeoutStopSec=20
KillMode=process
Restart=on-failure
RestartSec=5
 
[Install]
WantedBy=multi-user.target
'  
sudo systemctl daemon-reload  
sudo systemctl enable radarr  
sudo systemctl start radarr  
