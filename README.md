# Windows init

* Chocolatey
* Chrome
* 7+ Taskbar Tweaker
  * https://tweaker.rammichael.com/
* Visual Studio
* SSMS


# RPi

sudo apt update && sudo apt upgrade -y && sudo apt autoremove && sudo apt autoclean

sudo vim /etc/ssh/sshd_config

* VIM
  * sudo apt install vim -y

* Mount USB Drive
  * sudo apt-get install ntfs-3g
  * sudo mkdir /media/usb
  * sudo chmod 770 /media/usb
  * sudo mount /dev/sda1 /media/usb
  * sudo cp /etc/fstab /etc/fstab.backup
  * sudo vim /etc/fstab
    /dev/sda1 /media/usb ntfs defaults 0 0

* Samba
  * sudo apt install samba samba-common-bin -y
  * sudo vim /etc/samba/smb.conf
    [usb]
    path = /media/usb
    writeable=Yes
    create mask=0777
    directory mask=0777
    public=no
  * sudo smbpasswd -a pi
  * sudo systemctl restart smbd







