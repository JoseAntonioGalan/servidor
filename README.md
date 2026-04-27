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

```text
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
```
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

---

# 👥 Gestión de usuarios, grupos y permisos en Proxmox

```text
## 🔴 Grupo: admins
- Descripción: Administración total del sistema
- Rol: Administrador
- Ruta: /

- Usuario actual:
  - admin@pam

- Uso:
  - Gestión completa de Proxmox
  - Configuración de red, storage, backups
  - Gestión de usuarios

---

## 🟠 Grupo: devops
- Descripción: Gestión de infraestructura y despliegues
- Rol: PVEAdmin
- Ruta: /

- Usuario futuro:
  - devops@pam

- Uso:
  - Crear y gestionar VMs
  - Arrancar/parar sistemas
  - Acceso a consola
  - No acceso a usuarios ni sistema base

---

## 🟡 Grupo: developers
- Descripción: Acceso a máquinas y servicios
- Rol: PVEVMUser
- Ruta: /vms

- Usuario futuro:
  - dev1@pam

- Uso:
  - Acceso a VMs
  - Consola
  - Reinicio
  - Sin creación/eliminación de VMs

---

## 🟢 Grupo: readonly
- Descripción: Auditoría / solo lectura
- Rol: PVEAuditor
- Ruta: /

- Usuario futuro:
  - auditor@pam

- Uso:
  - Ver estado del sistema
  - Ver métricas
  - Sin cambios

---

## 🔵 Grupo: backup
- Descripción: Gestión de copias de seguridad
- Rol: PVEDatastoreAdmin
- Ruta: /storage

- Usuario futuro:
  - backup@pam

- Uso:
  - Gestionar backups
  - Restaurar máquinas
  - Acceso a almacenamiento

---

# 🧠 Configuración actual (solo tú)

Usuarios activos:

- admin@pam → grupo admins

👉 No necesitas crear los otros usuarios todavía
👉 Solo crea los grupos y deja los permisos preparados

---

# 🔐 Buenas prácticas

- Usar SIEMPRE grupos para permisos
- Activar 2FA en admin
- No usar root para operar
- No dar permisos globales innecesarios

---

# 🚀 Ventajas de este enfoque

- Escalas sin tocar permisos
- Añadir usuarios es inmediato
- Separación clara de responsabilidades
- Seguridad desde el inicio
```

---

# Gestion carpetas en bookstak

```text
📚 Infraestructura
 ├── Proxmox
 ├── Red
 ├── Storage
 └── Backups

📚 Kubernetes
 ├── Cluster
 ├── Deployments
 └── Ingress

📚 Servicios
 ├── Vaultwarden
 ├── Gitea
 ├── DB
 └── Apps

📚 Operación
 ├── Cómo desplegar
 ├── Cómo restaurar backups
 └── Troubleshooting
```
