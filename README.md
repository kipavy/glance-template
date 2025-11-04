1. Install Glance template on Dokploy or using https://community-scripts.github.io/ProxmoxVE/scripts?id=glance
2. provide following env variables in dokploy:
    - PROXMOX="user@pam!name=key"
    - PROXMOX_URI="https://192.168.1.75:8006" (your Proxmox server URL with protocol and port, no trailing slash)
    - TAILSCALE=API_KEY
    - SCRUTINY_URL=192.168.1.149:8087

3. open terminal in dokploy
4. 
    ```sh
    # FOR DOKPLOY
    apk add git
    cd /app
    git clone https://github.com/kipavy/glance-template
    cp -r glance-template/config .
    rm -rf glance-template
    ```
    ```sh
    # FOR PROXMOX VE SCRIPT
    apt install -y git
    cd /opt/glance
    git clone https://github.com/kipavy/glance-template
    cp -r glance-template/config/* .
    rm -rf glance-template
    ```
5. You can then replace my services with yours, for icons refer to https://github.com/glanceapp/glance/blob/main/docs/configuration.md#icons
    
    ![](dashboard.png)
