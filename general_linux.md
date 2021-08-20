## this and that
[Markdownguide](https://www.markdownguide.org/basic-syntax)

### get current kernel version
`uname -r` resulting in e.g. `5.8.0-63-generic`

### edit grub bootloader to skip fs checks every boot
- editing grub boot loader with `sudo nano /etc/default/grub` and changing: 
    ```
    GRUB_CMDLINE_LINUX_DEFAULT="fsck.mode=skip quiet splash"
    ```
- and then running `sudo update-grub`

### automount of Ubuntu using a-priori file manager access
[as explained in GIO-mount](https://wiki.ubuntuusers.de/gio_mount)  
alternatively, manual anonymous mount
`gio mount -a smb://SERVER/SHARE`
manual unmount
`gio mount -u smb://SERVER/SHARE`
location of samba share after mount:  
`ll ll /run/user/1000/gvfs/smb-share\:server\=SERVER\,share\=SHARE/`  
&rightarrow; not happy with the options and lack of backward-compatibility

### automount of samba share using scripting
[old-school cifs-mount](https://baihuqian.github.io/2019-10-20-how-to-mount-wd-mycloud-on-ubuntu-18-04)  
bla

### running session scripts
[source](https://unix.stackexchange.com/questions/172179/gnome-shell-running-shell-script-after-session-starts)  
running a script once after Gnome starts up, e.g. for mounting SMB-Shares.  
enter the `gnome-session-properties` configuration tool and add a script

---

## Coding
### setting up Git
- check status: `git config --list --show-origin`  
- add user name: `git config --global user.name "yourusername"`  
- add email: `git config --global user.email "email@youremail.com"`  
&rightarrow; settings will be added to ~/.gitconfig file

---

## Setting up SSH with keys
### adding hosts
`sudo nano /etc/hosts`

### generating keys on client
[source](https://www.ssh.com/academy/ssh/keygen#choosing-an-algorithm-and-key-size)  
`ssh-keygen -f ~/.ssh/ssh-key-ecdsa -t ecdsa -b 521`  
&rightarrow; supply secret key  
&leftarrow; public and private key generated

### deploying public key on server
`ssh-copy-id -i .ssh/ssh-key-ecdsa.pub USER@GUEST`  
&leftarrow; enter password for host  
&rightarrow; public key registered on host

### verifying key was deployed on server
`ssh user@server`  
&leftarrow; ubuntu asks for secret key - once

### secure copy a folder to destination
`scp -r $FOLDERNAME $SERVERUSER@$SERVERNAME:~`

---

## software bits and pieces
### install mesa tools for stuff like glx-info, glx-gears 
`sudo apt-get install mesa-utils`
### removing packages as completely as possible by example
```bash
sudo apt-get remove --purge libreoffice*
sudo apt-get clean
sudo apt-get autoremove
```
### getting Gnome software store back on a clean Ubuntu Focal Fossa 20.04
The standard software store has recently been changed to **Snap** which does not provide all the desired tools.  
`sudo apt-get --purge --reinstall install gnome-software`
### moving Gnome start button to opposite side of task bar
Task bar sits on the left border by default but can be moved to any edge in **Settings**, however the start button needs to be flipped by command line:  
`gsettings set org.gnome.shell.extensions.dash-to-dock show-apps-at-top true`

---

## enable battery charge limiter
[source](https://www.linuxuprising.com/2021/02/how-to-limit-battery-charging-set.html)

1. verify that `ls /sys/class/power_supply`  
    returns `AC0  BAT0`  
    and `ls /sys/class/power_supply/BAT*/charge_control_end_threshold`  
    returns an existing path - otherwise function does not exist

2. generate new file: `sudo nano /etc/systemd/system/battery-charge-threshold.service`
    ```
    [Unit]
    Description=Set the battery charge threshold
    After=multi-user.target
    StartLimitBurst=0

    [Service]
    Type=oneshot
    Restart=on-failure
    ExecStart=/bin/bash -c 'echo CHARGE_STOP_THRESHOLD > /sys/class/power_supply/BATTERY_NAME/charge_control_end_threshold'

    [Install]
    WantedBy=multi-user.target
    ```
    with `CHARGE_STOP_THRESHOLD` in [60,80,100] and `BATTERY_NAME = BAT0`

3. start and permanently enable service  
    `sudo systemctl enable battery-charge-threshold.service`  
    and  
    `sudo systemctl start battery-charge-threshold.service`

4. check status of charging service: `cat /sys/class/power_supply/BATTERY_NAME/status`  
    with `BATTERY_NAME = BAT0`

---
## enable multiple displays on ASUS TUF Gaming FX505DV-HN311T
[source 1, ](https://www.linuxbabe.com/desktop-linux/switch-intel-nvidia-graphics-card-ubuntu)
[source 2](https://www.reddit.com/r/Ubuntu/comments/laf04n/working_asus_tuf_a15_with_ubuntu_2004_rtx_2060)

System hosts two graphics cards:  
- Nvidia RTX 2060
- AMD/ATI Radeon RX Vega 10  

HDMI port apparently hard-wired to Nvidia card (other option would be to use the USB-C port)
1. check hardware specs: `lspci -k | grep -A 2 -i "VGA"`  
2. check more hardware specs: `xrandr`
3. check more hardware specs: `sudo lshw -C display`  
4. open up the app **Software & Updates**. Click the **Additional Drivers** tab (see, screen shot). You can see which driver is used for Nvidia card (Nouveau by default) and a list of additional proprietary drivers. Select the most recent tested proprietary driver, e.g. "nvidia-driver-470" and **Apply Changes**.

    > Ubuntu will install the drivers and the newest kernel (which had several issues of hardware features missing in my case). However selecting the old kernel in the **grub advanced boot loader** will still provide the driver modules. In my case the old/current/working kernel is `5.8.0-63-generic` and the updated/broken kernel was `5.11.0-25-generic`

5.  switching between card settings:
    - select AMD card: `sudo prime-select intel`  
    - select Nvidia card: `sudo prime-select nvidia`  
    - display current setting: `prime-select query`

    or using the **Nvidia App** and selecting the Prime Profile required  

6. remove new kernel that did not work properly:  
    `sudo apt remove linux-image-5.11.0-25-generic linux-image-unsigned-5.11.0-25-generic --verbose-versions`  
    Note: the Nvidia modules under `/lib/modules/5.11.0-25-generic/kernel` are still required

Screenshots:

![alt text][img01]

![alt text][img02]

![alt text][img03]

[img01]:  ./Pictures/2021-08-10_configuration.png "Display configuration"
[img02]:  ./Pictures/2021-08-10_settings.png "System settings"
[img03]:  ./Pictures/2021-08-10_updates.png "Driver update settings"
