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

## Install Code Server

You can also setup code server as a regular installation. The benifit to install in the local file system is that you can use the Visual Studio Code to test docker build

Here is the instruction:

```bash
# define a variable. Goto https://github.com/cdr/code-server/releases/ to find the version you want to install
CS_VERSION="3.4.1"
mkdir ~/code-server &&  cd ~/code-server
wget https://github.com/cdr/code-server/releases/download/$CS_VERSION/code-server-$CS_VERSION-linux-x86_64.tar.gz
tar -xzvf code-server-$CS_VERSION-linux-x86_64.tar.gz
rm code-server-$CS_VERSION-linux-x86_64.tar.gz
mv code-server-$CS_VERSION-linux-x86_64 /usr/lib/code-server
cd .. && rm -r code-server/
```

We have put the source code(bin) in correct folder. We need to create a folder to store code server configuration file (save as docker volume)
```bash
ln -s /usr/lib/code-server/code-server /usr/bin/code-server
mkdir /docker/appdata/codeserver
```

We need to create a deamon service to auto start the code server

```
nano /lib/systemd/system/code-server.service
```

And copy the following and save the data
```
[Unit]
Description=code-server
After=nginx.service

[Service]
Type=simple
Environment=PASSWORD=your_password
ExecStart=/usr/bin/code-server --bind-addr 127.0.0.1:8430 --user-data-dir /docker/appdata/codeserver --auth password
Restart=always

[Install]
WantedBy=multi-user.target
```
> --bind-addr: replace with your server's local ip address(access from LAN) or 0.0.0.0 (any address, not secure)
> Double check the port and user-data-dir to match your system. Also modify `your_password` to your desired password !important

We are almost there
```
systemctl start code-server

systemctl status code-server  # get status
# systemctl stop code-server  # stop the server
```


## Setup SSH 

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

## Setup Wildcard SSL Certificate

There are a lot of tools that can create Letsencrypt SSL certificate. If your ISP does NOT block port 80, skip this instruction. Othereise, you have to use DNS challenge to request a free SSL certificate. This document use cloudflare.com as DNS name server.

1. Install certbot. 

Switch to root `sudo su`
```
apt-get install certbot python-certbot-nginx
apt-get install python3-certbot-dns-cloudflare
```

2. Create an Ini File

```
mkdir /etc/letsencrypt
nano /etc/letsencrypt/cloudflare.ini
```

Paste the following into it
```
# Cloudflare API token used by Certbot
# dns_cloudflare_api_token = TryThisTokenMethodFirst

# Cloudflare API credentials used by Certbot
dns_cloudflare_email = your_cloudflare_email@gmail.com
dns_cloudflare_api_key = your_api_key
```

3. Create Certificate and Test

```
certbot certonly  \
    --dns-cloudflare \
    --dns-cloudflare-credentials /etc/letsencrypt/cloudflare.ini \
    --server https://acme-v02.api.letsencrypt.org/directory \
    -d '*.yourdomainname.com' \
    -d 'yourdomainname.com'
    
certbot renew --dry-run
```

## Setup Dockers
 
 1. Change OpenMediaVault web gui port (do NOT use port 80) and password
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

