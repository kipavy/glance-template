1. Install Glance template on Dokploy
2. provide following env variables in dokploy:
    - PROXMOX="user@pam!name=key"
    - TAILSCALE=API_KEY

3. open terminal in dokploy
4. 
    ```
    apk add git
    cd /app
    git clone https://github.com/kipavy/glance-template
    cp -r glance-template/* .
    rm -rf glance-template
    ```