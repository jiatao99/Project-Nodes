# How to install and config OMV

## Initial Setup

1. Install Debian Distro. Remember to create a new user and enable system util and ssh. Partition the main disk if necessary
2. Configure router for the fixed ip address of the new linux box
3. ssh into the new Linux box, 
    - https://openmediavault.readthedocs.io/en/5.x/installation/on_debian.html
    - wget -O - https://github.com/OpenMediaVault-Plugin-Developers/packages/raw/master/install | bash
    - reboot
 4. Update and install the OMV extras
 5. Install Docker and Portainers
 
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
 3. Install SFTP
 ```
 ---
version: "2"

services:
  sftp:
    image: atmoz/sftp
    container_name: sftp
    volumes:
        - /srv/disk1/ftp:/home/jiatao
    ports:
        - "2222:22"
    command: {userName}:{Password}:{UID}:{GID}
 ```
 
 
