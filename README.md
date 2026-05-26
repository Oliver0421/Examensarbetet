# Examensarbetet

## Så här är docker-compose.yml filen 

### Vissa credentials är borta från Plex, Vaultwarden b.l.a

#### Om man vill testa yml filen så 

#### Homarr behöver en crypterings nyckel så 

` openssl rand -hex 32 ` I terminalen 

### Lägg sedan till den i kommande SECRET_ENCRYPTION

### Viktiga delar är image. Hitta befintliga uppdaterade images från docker hub, ifall det inte fungerar

### Vad jag lade till i Docker Compose 

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
## Slutet 👍

## Eller lite mer 

```
ollama:
    networks:
      - homelab
    image: ollama/ollama:latest
    container_name: ollama
    ports:
      - "11434:11434"
    volumes:
      - ollama_data:/root/.ollama
    environment:
      - OLLAMA_MAX_LOADED_MODELS=1
      - OLLAMA_NUM_PARALLEL=1
    entrypoint: ["/bin/sh", "-c", "ollama serve & sleep 10 && ollama pull llama3 && wait"]   
    
    restart: unless-stopped

```


```
 open-webui:
    networks:
      - homelab
    image: ghcr.io/open-webui/open-webui:main
    container_name: open-webui
    ports:
      - "3000:8080"
    environment:
      - OLLAMA_BASE_URL=http://ollama:11434

    volumes:
      - openwebui_data:/app/backend/data
    depends_on:
      - ollama
    restart: unless-stopped
```

### Ollama till grafiskt gränssnitt 

