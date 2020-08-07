# How to Install and Configure OpenMediaVault

## Initial Setup

1. Install Debian Distro. Remember to create a new user and enable system util and ssh. Partition the main disk if necessary
2. Configure router for the fixed ip address of the new linux box
3. ssh into the new Linux box. Follow instruction here https://openmediavault.readthedocs.io/en/5.x/installation/on_debian.html to install
    > if you get this error `omv-confdbadm command not found` then do this:
    
    ```bash
    export PATH=$PATH:/usr/sbin
    omv-confdbadm populate
    ```
4. Reboot
5. Install the OMV extras
    - `wget -O - https://github.com/OpenMediaVault-Plugin-Developers/packages/raw/master/install | bash`
    
## Fix Debian Dependency Hardware Issue

1. Add *contrib non-free* in apt source list
```bash
sudo nano /etc/apt/sources.list
apt-get update
```
  - for ```TSC_DEADLINE disabled due to Errata; please update microcode to version: 0x25``` error: ```sudo apt-get install intel-microcode```
  - for ```possible missing rtl_nic/rtl8125a-3.fw``` error: 
    ```
    cd /lib/firmware/rtl_nic
    wget https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/plain/rtl_nic/rtl8125a-3.fw
    update-initramfs -u
    ```
2. To check stutus, run ```sudo dmesg```

> For ```firmware: failed to load regulatory.db (-2)```: it is a wireless card. Ignore for now
> for ```failed to load ar3k/ramps_0x31010000_40.dfu (-2)```: it is a bluetooth card. Ignore for now

## Use WebGui Tool    

1. General setting: change port and admin password
2. Network settings -> Interfaces, setup DNS server
3. Update management -> Update
4. OMV-Extras -> Install Docker and Portainers
5. Plugins -> Install plugins: SFTP, AutoShutdown, Remote Mount, RootFS Share, ResetPermissions, Symbolic Links
 
## Setup Dockers
 
 1. Change main web gui port (do NOT use port 80) and password
 2. Install Nginx Proxy Manager
 ```
version: '2'
services:
  app:
    image: jlesage/nginx-proxy-manager
    container_name: nginx-proxy
    environment:
      - USER_ID=1000
      - GROUP_ID=1000
    ports:
      - '80:8080'
      - '8181:8181'
      - '443:4443'
    volumes:
      - /srv/disk1/appdata/nginxproxy:/config
 ```

 4. Code Server
 ```
 ---
version: "2.1"
services:
  code-server:
    image: linuxserver/code-server
    container_name: code-server
    environment:
      - PUID=0.   # use root:root to avoid permission issue
      - PGID=0
      - PASSWORD=  ## your strong password to login into the web gui tool
      - UMASK=022
      - TZ=America/New-York
    volumes:
      - /srv/data/appdata/codeserver:/config
      - /srv/data:/data
    ports:
      - 8430:8443
    restart: unless-stopped
```

4. Setup SSH 

```bash
# generate rsa private, public key pair and save it under .ssh folder (mac/linux)
ssh-keygen -t rsa

# create remote .ssh folder for user. make sure the user is in group of ssh
ssh {user}@{server} mkdir -p .ssh

# Copy pub key to remote server
cat .ssh/id_rsa.pub | ssh {user}@{server} 'cat >> .ssh/authorized_keys'

# test
ssh {user}@{server}
```
