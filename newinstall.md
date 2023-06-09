# New installation of Ubuntu Jammy Jellyfish 22.04 LTS

## Packages

### Firefox

`sudo add-apt-repository ppa:mozillateam/ppa`
`sudo apt update`
`sudo apt install firefox-esr`

#### Basic configuration

1. Sign-in to sync via burger menu's `Settings`
2. In `Settings`, set `Website appearance` to `Dark`
3. In `Settings`, navigate to `Extensions & Themes` in `Themes` enable `Dark` theme

#### Enable smaller bookmarks

a) Enter `about:config` and set `browser.compactmode.show=true`
b) Right-click menu bar and select `Customize Toolbar`
c) In the bottom menu bar set `Density` option to `Compact (not supported)`

### Snap store

1. List versions via `snap list --all`
2. Restrict number of historic versions to keep in store `sudo snap set system refresh.retain=2`
3. Add shell script to `~/bin` and make executable using `chmod +x clean_snap.sh`

    ```bash
    #!/bin/bash
    #Removes old revisions of snaps
    #CLOSE ALL SNAPS BEFORE RUNNING THIS
    set -eu
    LANG=en_US.UTF-8 snap list --all | awk '/disabled/{print $1, $3}' |
        while read snapname revision; do
            snap remove "$snapname" --revision="$revision"
        done
    ```

4. Invoke using `sudo ./clean_snap.sh`

### Git

1. Install using `sudo apt-get install git-all`
2. Check version `git version`

#### Within a repo

- check status: `git config --list --show-origin`  
- add user name: `git config --global user.name "yourusername"`  
- add email: `git config --global user.email "email@youremail.com"`  
&rightarrow; settings will be added to ~/.gitconfig file

### Install VS Code

[source](https://code.visualstudio.com/docs/setup/linux)

1. Prerequisites

    ```bash
    sudo apt-get install wget gpg
    wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
    sudo install -D -o root -g root -m 644 packages.microsoft.gpg /etc/apt/keyrings/packages.microsoft.gpg
    sudo sh -c 'echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" > /etc/apt/sources.list.d/vscode.list'
    rm -f packages.microsoft.gpg
    ```

2. Install

    ```bash
    sudo apt install apt-transport-https
    sudo apt update
    sudo apt install code
    ```

3. Configuration by starting VS Code and signing in to the sync option using Github account  
&rightarrow; extensions and settings will be added from my configuration
