## FileBrowsers

```yml
---
version: "2.1"
services:
  filebrowser:
    image: 80x86/filebrowser:latest
    container_name: filebrowser
    environment:
      - PUID=998
      - PGID=100
      - TZ=America/New-York
      - WEB_PORT=8085
    volumes:
      - /srv/0440221b-577e-4a62-9c11-88c157fd3715/AppData/FileBrowser:/config
      - /srv/0440221b-577e-4a62-9c11-88c157fd3715/:/myfiles
    ports:
      - 8085:8085
    restart: unless-stopped
```
    
## DoubleCommander
    
```yml
---
version: "2.1"
services:
  doublecommander:
    image: linuxserver/doublecommander
    container_name: doublecommander
    environment:
      - PUID=998
      - PGID=100
      - TZ=Europe/London
    volumes:
      - /srv/0440221b-577e-4a62-9c11-88c157fd3715/AppData/doublecommander:/config
      - /srv/0440221b-577e-4a62-9c11-88c157fd3715:/data
    ports:
      - 3000:3000
    restart: unless-stopped
    ```
