# Examensarbetet

## Så här är docker-compose.yml filen 

### Vissa credentials är borta från Plex, Vaultwarden b.l.a

#### Om du vill testa yml filen så 

#### Homarr behöver en crypterings nyckel så 

` openssl rand -hex 32 ` I terminalen 

### och lägg till den här 

```
 homarr:
    image: ghcr.io/homarr-labs/homarr:latest
    container_name: homarr
    restart: unless-stopped
    networks:
      - homelab
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro   
      - ./data/homarr/appdata:/appdata
    environment:
      - SECRET_ENCRYPTION_KEY="SE MIG!"  
      - TZ=Europe/Stockholm
    ports:
      - "7575:7575"
```

### Plex behöver en token från [plex.tv/claim](https://plex.tv/claim)

```
plex:
    image: lscr.io/linuxserver/plex:latest
    container_name: plex
    restart: unless-stopped
    networks:
      - homelab
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Stockholm
      - VERSION=docker
      - PLEX_CLAIM="SE MIG!"      
    volumes:
      - ./data/plex/config:/config
      - ./data/plex/transcode:/transcode
      - ./data/plex/media:/media  # obs är media folder där all media kommer att sparas 
    ports:
      - "32400:32400"
```
### Vaultwarden behöver en säker https connection (det här gjorde inte jag) men en domän behöver den för att fungera. 

```
vaultwarden:
    image: vaultwarden/server:latest
    container_name: vaultwarden
    restart: unless-stopped
    networks:
      - homelab
    volumes:
      - ./data/vaultwarden:/data
    environment:
      - DOMAIN=http://localhost:8080     # Ska ändras till något HTTPS URL
      - SIGNUPS_ALLOWED=true             # True i början men efter att man har logat in så kan man ta bort den 
      - TZ=Europe/Stockholm
    ports:
      - "8080:80"

```
## Det var nog det 👍👌👊 : )
