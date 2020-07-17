## Heimdall

```yml
---
version: "2.1"
services:
  heimdall:
    image: linuxserver/heimdall:latest
    container_name: heimdall
    environment:
      - PUID=998
      - PGID=100
      - TZ=America/New-York
    volumes:
      - /srv/0440221b-577e-4a62-9c11-88c157fd3715/AppData/heimdall:/config
    ports:
      - 80:80
    restart: unless-stopped
```

## nextData

```yml
version: '2'
services:
  netdata:
    image: netdata/netdata
    container_name: netdata
    hostname: 192.168.1.68 # set to fqdn of host
    ports:
      - 19999:19999
    cap_add:
      - SYS_PTRACE
    security_opt:
      - apparmor:unconfined
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro
```
