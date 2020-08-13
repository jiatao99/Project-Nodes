### MariaDB

First create mario db

```yml
---
version: "2.1"
services:
  mariadb:
    image: linuxserver/mariadb
    container_name: mariadb
    environment:
      - PUID=998
      - PGID=100
      - MYSQL_ROOT_PASSWORD=sup1rPassw0rd
      - TZ=America/New-York
    volumes:
      - /srv/dev-disk-by-label-Data2/AppData/mariadb:/config
    ports:
      - 3306:3306
    restart: unless-stopped
```

### Bookstack

Reference the previous Docker using `DB_HOST` with `DBL_ROOT_PASSWORD`. Supply `DB_DATABASE`, `DB_USER`, `DB_PASS` will automatically create database.
28


```yml
---
version: "2"
services:
  bookstack:
    image: linuxserver/bookstack
    container_name: bookstack
    environment:
      - PUID=998
      - PGID=100
      - DB_HOST=0.0.0.0:3306
      - DBL_ROOT_PASSWORD: sup1rPassw0rd
      - DB_DATABASE=bookstack
      - DB_USER=bs_user
      - DB_PASS=bs_pass123
    volumes:
      - /srv/dev-disk-by-label-Data2/AppData/bookstack:/config
    ports:
      - 6875:80
    restart: unless-stopped
```
