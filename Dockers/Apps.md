## JellyFin

```yml
---
version: "2.1"
services:
  jellyfin:
    image: linuxserver/jellyfin
    container_name: jellyfin
    environment:
      - PUID=998
      - PGID=100
      - TZ=America/New-York
      - UMASK_SET=022 #optional
    volumes:
      - /srv/6ba672d5-3e36-4f69-a236-810072519436/Shared Videos:/data
      - /srv/0440221b-577e-4a62-9c11-88c157fd3715/AppData/JellyFin:/config
    ports:
      - 8096:8096
    restart: unless-stopped
```    

## Calibre

```yml
---
version: "2.1"
services:
  calibre:
    image: linuxserver/calibre
    container_name: calibre
    environment:
      - PUID=998
      - PGID=100
      - TZ=America/New-York
    volumes:
      - /srv/0440221b-577e-4a62-9c11-88c157fd3715/AppData/Calibre:/config
      - /srv/0440221b-577e-4a62-9c11-88c157fd3715/Public/eBooks:/books
    ports:
      - 8088:8080
      - 8081:8081
    restart: unless-stopped
```
