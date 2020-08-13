
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

I fotget to backup my Mariadb and the system crashed. I have to reinstall everything. This time is very painful to let nextcloud scan all the file changed.

1. Login to nextcloud docker

```
docker exec -it --user www-data nextcloud bash
```

2. Set maintainess mode
```
php occ maintenance:mode --on
php occ maintenance:mode --off
```

3. Scan the new file
```
# Very slow 
www-data php occ files:scan --all
```


