1. Se tendrá que crear una maquina virtual con la imagen de debian12

Pasos instalación mas adelante

2. Se tendrá que actualizar los repos y distros

```bash
apt update && apt upgrade
```

3. Se tendrá que instalar todas lo necesario para gestionar la template

```bash
apt update && apt install -y qemu-guest-agent cloud-init cloud-guest-utils sudo curl wget git vim nano htop net-tools iproute2 dnsutils iputils-ping bash-completion unzip rsync ca-certificates gnupg lsb-release
```

4. Se tendrá que ejecutar éste bloque final

```bash
cloud-init clean --logs

truncate -s 0 /etc/machine-id
rm -f /var/lib/dbus/machine-id
ln -sf /etc/machine-id /var/lib/dbus/machine-id

rm -f /etc/ssh/ssh_host_*
apt clean
apt autoremove -y
```
