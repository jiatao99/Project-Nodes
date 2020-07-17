## pyLoad

```yml
---
version: "2.1"
services:
  pyload:
    image: linuxserver/pyload
    container_name: pyload
    environment:
      - PUID=998
      - PGID=100
      - TZ=America/New-York
    volumes:
      - /srv/0440221b-577e-4a62-9c11-88c157fd3715/AppData/pyload:/config
      - /srv/0440221b-577e-4a62-9c11-88c157fd3715/Downloads:/downloads
    ports:
      - 8001:8000
      - 7227:7227 #optional
    restart: unless-stopped
```    
