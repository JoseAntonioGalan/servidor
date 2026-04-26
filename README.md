Distribucion del servidor

```text
- Proxmox host: 4–6 GB RAM (reservado)
-- SO: Proxmox VE (basado en Debian)

-- VM1: k3s-master-01: 2 vCPU / 4 GB RAM / 35–40 GB
--- SO: Debian 12 minimal
--- Servicios:
---- k3s server (control plane)
---- Ingress (Traefik o NGINX)
---- cert-manager

-- VM2: k3s-worker-01: 3 vCPU / 6 GB RAM / 45–60 GB
--- SO: Debian 12 minimal
--- Servicios:
---- k3s agent
---- microservicios (pods)

-- VM3: k3s-worker-02: 3 vCPU / 6 GB RAM / 45–60 GB
--- SO: Debian 12 minimal
--- Servicios:
---- k3s agent
---- microservicios (pods)

-- VM4: db-server: 4 vCPU / 8 GB RAM / 60–80 GB
--- SO: Debian 12 minimal
--- Servicios:
---- PostgreSQL
---- backups de bases de datos
---- firewall (acceso solo desde red interna)

-- VM5: infra-tools: 2 vCPU / 4 GB RAM / 30–40 GB
--- SO: Debian 12 minimal
--- Servicios:
---- Vaultwarden (gestión de contraseñas)
---- WireGuard / Tailscale (VPN)
---- Uptime Kuma (monitorización básica)
---- Gitea (repositorios Git)
---- BookStack / Wiki.js (documentación)
---- Caddy o Nginx Proxy Manager (reverse proxy)

- HDD 1TB:
-- Backups de Proxmox
-- Backups de bases de datos
-- ISOs y templates
-- Backups de Vaultwarden
```
