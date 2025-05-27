
# FASE 0 – BLOQUE 2: Hardening básico y preparación del sistema

## 🎯 Objetivo

Aplicar configuraciones de seguridad inicial, crear usuario técnico, y preparar los sistemas con herramientas esenciales para continuar con la instalación de Wazuh y OpenSearch.

---

## 🧱 1. Actualización del sistema operativo (ambas VMs)

```bash
sudo dnf update -y
```

---

## 🧰 2. Instalación de herramientas esenciales (ambas VMs)

```bash
sudo dnf install curl wget git vim unzip net-tools epel-release -y
```

Estas herramientas se usan para descarga, edición, gestión de red, etc.

---

## 👤 3. Crear usuario técnico con permisos administrativos (ambas VMs)

```bash
sudo useradd -m siemadmin
sudo passwd siemadmin
sudo usermod -aG wheel siemadmin
```

El usuario `siemadmin` será el administrador de las configuraciones en el entorno SIEM.

---

## 🔥 4. Activar y configurar firewall (ambas VMs)

```bash
sudo systemctl enable --now firewalld
sudo firewall-cmd --permanent --add-port={22,9200,9300,5601}/tcp
sudo firewall-cmd --reload
```

Esto permite conectividad para SSH, OpenSearch y el Dashboard de Wazuh.

---

## 💾 5. Montar disco adicional (solo en VM2)

### Verificar disco:

```bash
lsblk
```

### Formatear y montar:

```bash
sudo mkfs.xfs /dev/sdb
sudo mkdir -p /mnt/data/opensearch
sudo mount /dev/sdb /mnt/data/opensearch
```

### Agregar a `/etc/fstab` para montaje automático:

```bash
echo '/dev/sdb /mnt/data/opensearch xfs defaults 0 0' | sudo tee -a /etc/fstab
```

---

## ✅ Validación del Bloque 2

- [ ] El sistema está actualizado correctamente en ambas VMs (`dnf update` ejecutado sin errores)
- [ ] Herramientas esenciales (`curl`, `wget`, `git`, `vim`, etc.) están instaladas y funcionales (`which curl`, `git --version`)
- [ ] Usuario `siemadmin` existe y tiene permisos sudo (`id siemadmin` y `sudo su - siemadmin`)
- [ ] `firewalld` está activo y los puertos 22, 9200, 9300, 5601 están abiertos (`firewall-cmd --list-ports`)
- [ ] En VM2, el disco `/dev/sdb` está montado en `/mnt/data/opensearch` y figura en `df -h` y `/etc/fstab`

---

📘 Continúa con el Bloque 3 para documentar la preparación de GitHub y estructura de control de versiones.
