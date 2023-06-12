# Linux, Ubuntu 20.04 LTS, and related tidbits

## Report current kernel version

`uname -r` resulting in e.g. `5.8.0-63-generic`

## Access the boot process log

`journalctl -b`

## Edit the grub bootloader to skip fs checks every boot

- editing grub boot loader with `sudo nano /etc/default/grub` and changing:

    ```[bash]
    GRUB_CMDLINE_LINUX_DEFAULT="fsck.mode=skip quiet splash"
    ```

- and then running `sudo update-grub`

## Automount of NAS shares on Ubuntu

Option 1: using a-priori file manager access [as explained in GIO-mount.](https://wiki.ubuntuusers.de/gio_mount)  

- alternatively, manual anonymous mount  
  `gio mount -a smb://SERVER/SHARE`

- manual unmount  
  `gio mount -u smb://SERVER/SHARE`

- location of samba share after mount:  
  `/run/user/1000/gvfs/smb-share\:server\=SERVER\,share\=SHARE/`

&rightarrow; not happy with the options and lack of backward-compatibility

Option 2: running *old-school* mount scripts from my `~/bin` for connecting when required:
[source](https://baihuqian.github.io/2019-10-20-how-to-mount-wd-mycloud-on-ubuntu-18-04)  
My script tests for the availability of the NAS server on the local network and aborts in case
of being on the road with my laptop (no VPN connection back home, so far).
This can be further refined using variables and loops in case of many shares needing connecting.

``` {bash}
#!/bin/bash
if ping -c 1 exampleserver &> /dev/null
then
  if [ ! -d /run/user/1000/gvfs/smb-share\:server\=exampleserver\,share\=exampleshare ]
  then
    echo "... mounting SMB-share exampleshare on exampleserver"
    gio mount -a smb://exampleserver/exampleshare
  else
    echo "... mount already exists - do nothing"
  fi
  if [ ! -d ~/exampleshare ]
  then
    echo "... linking SMB-share to ~/exampleshare"
    ln -s /run/user/1000/gvfs/smb-share\:server\=exampleserver\,share\=exampleshare ~/exampleshare
  else
    echo "... link already exists - do nothing"
  fi
else
  echo "... host exampleserver not found - do nothing"
fi
```

Option 3: using the *CIFS*-package which lets me access shares protected by authentication,
which I didn't get to work with the above methods.  
[source](https://wiki.ubuntuusers.de/mount.cifs/)

- install the package  
  `sudo apt install -y cifs-utils`

- create a credentials file in user home and create mount point  
  `touch .smbcredentials && mkdir examplemountpoint`  
  the file should contain the following login data adjusted to your requirements:

  ``` {bash}
  username=smbexampleuser
  password=smbexamplepassword
  domain=WORKGROUP
  ```

- add an entry to `/etc/fstab`:

  ``` {bash}
  #SMB mount for mycloud share
  //exampleserver/exampleshare /home/exampleuser/examplemountpoint cifs rw,_netdev,noauto,user,credentials=/home/exampleuser/.smbcredentials  0  0
  ```

  which allows a user/owner of the mountpoint to mount and unmount the share read/write using:  
  `mount ~/examplemountpoint` and `umount ~/examplemountpoint`

### Running session scripts

[source](https://unix.stackexchange.com/questions/172179/gnome-shell-running-shell-script-after-session-starts)  
running a script once after Gnome starts up, e.g. for mounting SMB-Shares.  
enter the `gnome-session-properties` configuration tool and add a script

---

## Coding / Software development / Data Analysis Tools

### setting up Git

- check status: `git config --list --show-origin`  
- add user name: `git config --global user.name "yourusername"`  
- add email: `git config --global user.email "email@youremail.com"`  
&rightarrow; settings will be added to ~/.gitconfig file

---

## Setting up SSH with keys

This is fairly old knowledge from the time when did have to do these things manually.

1. adding hosts  
`sudo nano /etc/hosts`

2. generating keys on client [source](https://www.ssh.com/academy/ssh/keygen#choosing-an-algorithm-and-key-size)  
`ssh-keygen -f ~/.ssh/ssh-key-ecdsa -t ecdsa -b 521`  
&rightarrow; supply secret key  
&leftarrow; public and private key generated

3. deploying public key on server  
`ssh-copy-id -i .ssh/ssh-key-ecdsa.pub USER@GUEST`  
&leftarrow; enter password for host  
&rightarrow; public key registered on host

4. verifying key was deployed on server  
`ssh user@server`  
&leftarrow; ubuntu asks for secret key - once

5. secure copy a folder to destination  
`scp -r $FOLDERNAME $SERVERUSER@$SERVERNAME:~`
