# 🖥️ Infraestructura inicial - Servidor pequeño

## 🎯 Objetivo

Montar una infraestructura base para:

- Microservicios (Kubernetes - k3s)
- Base de datos centralizada
- Herramientas internas (Git, documentación, contraseñas)
- Acceso seguro (VPN)
- Monitorización básica
- Base para CI/CD futuro

---

# 🧱 Arquitectura general

- Proxmox host: 4–6 GB RAM (reservado)
-- SO: Proxmox VE (basado en Debian)

-- VM1: k3s-master-01: 2 vCPU / 4 GB RAM / 35–40 GB
--- SO: Debian 12 o Ubuntu Server 24.04 LTS
--- Servicios:
---- k3s server (control plane)
---- Ingress (Traefik o NGINX)
---- cert-manager

-- VM2: k3s-worker-01: 3 vCPU / 6 GB RAM / 45–60 GB
--- SO: Debian 12 o Ubuntu Server 24.04 LTS
--- Servicios:
---- k3s agent
---- microservicios (pods)

-- VM3: k3s-worker-02: 3 vCPU / 6 GB RAM / 45–60 GB
--- SO: Debian 12 o Ubuntu Server 24.04 LTS
--- Servicios:
---- k3s agent
---- microservicios (pods)

-- VM4: db-server: 4 vCPU / 8 GB RAM / 60–80 GB
--- SO: Debian 12
--- Servicios:
---- PostgreSQL (recomendado) o MariaDB
---- Backups de bases de datos
---- Firewall (solo acceso interno)

-- VM5: infra-tools: 2 vCPU / 4 GB RAM / 30–40 GB
--- SO: Debian 12
--- Servicios:
---- Vaultwarden (gestión de contraseñas)
---- Tailscale (VPN)
---- Uptime Kuma (monitorización básica)
---- Gitea (repositorios Git)
---- BookStack (documentación)
---- Nginx Proxy Manager o Caddy (reverse proxy)

---

# 💾 Almacenamiento

## NVMe (256 GB)
- Sistema Proxmox
- Discos de las VMs
- Microservicios y DB

## HDD (1 TB)
- Backups de Proxmox
- Backups de bases de datos
- Backups de Vaultwarden
- ISOs y templates

---

# 🔐 Seguridad

- Acceso a infraestructura SOLO por VPN (Tailscale)
- No exponer servicios directamente a Internet
- Firewall activo en Proxmox y VMs
- 2FA en Vaultwarden
- Acceso a DB restringido a red interna

---

# 🔑 Gestión de contraseñas

- Vaultwarden como fuente de verdad
- Estructurar credenciales por:
  - Infraestructura
  - Bases de datos
  - Aplicaciones
- Las apps usan:
  - Kubernetes Secrets
  - (Más adelante: SOPS + Git)

---

# 🌐 Acceso a servicios

Uso de reverse proxy:

Ejemplo:

- https://vault.lab.local → Vaultwarden
- https://git.lab.local → Gitea
- https://monitor.lab.local → Uptime Kuma
- https://app.lab.local → aplicaciones en k3s

---

# 📡 VPN

Herramienta recomendada:

- Tailscale

Uso:

- Acceso seguro a toda la infraestructura
- Sin abrir puertos
- Acceso desde portátil/equipo de trabajo

---

# 📊 Monitorización

## Actual (fase 1)

- Uptime Kuma
  - Monitorización de:
    - VMs
    - servicios web
    - base de datos
    - endpoints

## Futuro (fase 2)

- Prometheus + Grafana + Loki (dentro de k3s)

---

# 💾 Backups (CRÍTICO)

## Qué respaldar

- VMs (Proxmox backups)
- Base de datos (dump diario)
- Vaultwarden (/data)
- Gitea

## Estrategia mínima

- Backup diario → HDD
- Backup semanal → externo (USB o segundo servidor)

---

# 🚀 Orden de despliegue

1. Proxmox
2. VM infra-tools
3. VPN (Tailscale)
4. Vaultwarden
5. VM db-server
6. Cluster k3s
7. Ingress + cert-manager
8. Gitea
9. Backups
10. Uptime Kuma

---

# ⚠️ No implementar todavía

- Ceph
- HA Kubernetes
- Longhorn
- HashiCorp Vault
- Service Mesh (Istio)
- SonarQube
- Nexus
- Prometheus completo

---

# 🧾 Conclusión

Infraestructura diseñada para:

- Simplicidad
- Estabilidad
- Escalabilidad futura

Base sólida para evolucionar hacia:

- CI/CD
- Observabilidad completa
- Alta disponibilidad (cuando haya más hardware)
