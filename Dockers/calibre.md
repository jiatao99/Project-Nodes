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
