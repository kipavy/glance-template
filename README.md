1. Install Glance, either with **Docker Compose** (what I run on my NAS, see below) or with the Proxmox VE script: https://community-scripts.github.io/ProxmoxVE/scripts?id=glance
2. Provide the following env variables in the `.env` next to your `docker-compose.yml`, or in `/opt/glance/.env` for Proxmox VE Script deployment:
    ```
    PROXMOX_URL=pve.lan:8006
    PROXMOX_TOKEN="user@pam!name=key"
    TAILSCALE_API_KEY=API_KEY
    SCRUTINY_URL=docker.lan:8087
    IMMICH_URL=docker.lan:2283
    IMMICH_API_KEY=API_KEY
    ```
    Hosts are `<container>.lan` names that AdGuard Home serves from the Proxmox config
    ([pve-dns-sync](https://github.com/kipavy/Wiki/blob/main/scripts/pve-dns-sync.sh)), so nothing here changes when the server moves to another network.

3. ### For Docker Compose
    `docker-compose.yml` (the Docker socket is only needed for the `docker-containers` widget):
    ```yaml
    services:
      glance:
        image: glanceapp/glance
        restart: unless-stopped
        volumes:
          - ./files/app/config/:/app/config
          - ./files/app/assets:/app/assets
          - /var/run/docker.sock:/var/run/docker.sock:ro
        ports:
          - 9998:8080
        env_file: .env
    ```
    Start it, then pull the template config into the running container. Run the same command again whenever you want to update, Glance reloads the config on the fly:
    ```sh
    docker compose up -d
    docker compose exec -T glance sh -c 'apk add --no-cache git && cd /app && git clone https://github.com/kipavy/glance-template && cp -r glance-template/config . && rm -rf glance-template'
    ```
    The config lands in `./files/app/config` on the host (bind mount), so it survives container recreation.
    ### For Proxmox VE Script
    ```sh
    # FOR PROXMOX VE SCRIPT
    apt install -y git
    cd /opt/glance
    git clone https://github.com/kipavy/glance-template
    cp -r glance-template/config/* .
    rm -rf glance-template

    # Just adding .env in glance service
    cat > /etc/systemd/system/glance.service <<EOF
    [Unit]
    Description=Glance Daemon
    After=network.target

    [Service]
    Type=simple
    EnvironmentFile=/opt/glance/.env
    WorkingDirectory=/opt/glance
    ExecStart=/opt/glance/glance --config /opt/glance/glance.yml
    TimeoutStopSec=20
    KillMode=process
    Restart=on-failure

    [Install]
    WantedBy=multi-user.target
    EOF
    ```
4. You can then replace my services with yours, for icons refer to https://github.com/glanceapp/glance/blob/main/docs/configuration.md#icons
    (`sh:` and `di:` icons are SVG by default, add `.png` or `.webp` when the icon only exists in that format, e.g. `sh:dockhand.png`)
    
    ![](dashboard.png)
