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


### NextCloud

Reference the previous Docker using `MYSQL_HOST` with `MYSQL_ROOT_PASSWORD`. Supply `MYSQL_DATABASE`, `MYSQL_USER`, `MYSQL_PASSWORD` will automatically create database.

```yml
---
version: "2.1"
services:
  app:
    image: nextcloud
    container_name: nextcloud
    ports:
      - 7560:80
    environment:
      - MYSQL_HOST=0.0.0.0:3306 
      - MYSQL_ROOT_PASSWORD=sup1rPassw0rd
      - MYSQL_PASSWORD=nc_pass123
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nc_user1
      - TZ=America/New-York
    volumes:
      - /srv/dev-disk-by-label-Data2/AppData/nextcloud:/var/www/html
    restart: always
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
