# Windows
* Chocolatey
* Chrome
* 7+ Taskbar Tweaker
  * https://tweaker.rammichael.com/
* Visual Studio
* SSMS


# RPi
sudo apt update && sudo apt upgrade -y && sudo apt autoremove && sudo apt autoclean
sudo vim /etc/ssh/sshd_config
sudo apt install apt-transport-https dirmngr gnupg ca-certificates
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
echo "deb https://download.mono-project.com/repo/debian stable-raspbianbuster main" | sudo tee /etc/apt/sources.list.d/mono-official-stable.list
sudo apt update
sudo apt install mono-complete


### VIM
sudo apt install vim -y

### Mount USB Drive
sudo apt-get install ntfs-3g
sudo mkdir /media/usb
sudo chmod 770 /media/usb
sudo mount /dev/sda1 /media/usb
sudo cp /etc/fstab /etc/fstab.backup
sudo vim /etc/fstab  
`/dev/sda1 /media/usb ntfs defaults 0 0`

### Samba
sudo apt install samba samba-common-bin -y
sudo vim /etc/samba/smb.conf
`[usb]  
   path = /media/usb  
   writeable=Yes  
   create mask=0777  
   directory mask=0777  
   public=no`
* sudo smbpasswd -a pi
* sudo systemctl restart smbd

### Transmission
sudo apt install transmission-daemon -y
sudo systemctl stop transmission-daemon
mkdir -p /media/usb/torrent-inprogress
mkdir -p /media/usb/torrent-complete
sudo chown -R pi:pi /media/usb/torrent-inprogress
sudo chown -R pi:pi /media/usb/torrent-complete
sudo vim /etc/transmission-daemon/settings.json
`"incomplete-dir": "/media/usb/torrent-inprogress",  
"download-dir": "/media/usb/torrent_complete",  
"incomplete-dir-enabled": true,  
"rpc-password": "Your_Password",  
"rpc-username": "Your_Username",  
"rpc-whitelist": "192.168.*.*",`
sudo vim /etc/init.d/transmission-daemon  
`USER=pi`
* sudo vim /etc/systemd/system/multi-user.target.wants/transmission-daemon.service
`user=pi`
* sudo systemctl daemon-reload
* sudo chown -R pi:pi /etc/transmission-daemon
* sudo mkdir -p /home/pi/.config/transmission-daemon/
* sudo ln -s /etc/transmission-daemon/settings.json /home/pi/.config/transmission-daemon/
* sudo chown -R pi:pi /home/pi/.config/transmission-daemon/
* sudo systemctl start transmission-daemon

### NZBGET
























