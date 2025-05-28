# FASE 1 – BLOQUE 1: Instalación de Wazuh Manager (VM1)

## 🎯 Objetivo del bloque
Instalar correctamente el servicio Wazuh Manager desde el repositorio oficial, dejando el servicio operativo en la VM1 como nodo central del SIEM.

---

## 📋 Requisitos previos

- Sistema operativo: Rocky Linux 9
- Acceso root a la máquina
- Conectividad a internet
- DNS configurado manualmente (nameserver 8.8.8.8)

---

## ⚙️ Pasos ejecutados

### 1. Agregar DNS funcional para resolución
```bash
echo "nameserver 8.8.8.8" > /etc/resolv.conf
```

### 2. Crear archivo del repositorio de Wazuh
```bash
cat > /etc/yum.repos.d/wazuh.repo << EOF
[wazuh_repo]
gpgcheck=1
gpgkey=https://packages.wazuh.com/key/GPG-KEY-WAZUH
enabled=1
name=Wazuh repository
baseurl=https://packages.wazuh.com/4.x/yum/
protect=1
EOF
```

### 3. Importar la clave GPG
```bash
rpm --import https://packages.wazuh.com/key/GPG-KEY-WAZUH
```

### 4. Instalar Wazuh Manager
```bash
dnf clean all
dnf install wazuh-manager -y
```

### 5. Habilitar y arrancar el servicio
```bash
systemctl daemon-reexec
systemctl enable wazuh-manager --now
```

---

## ✅ Validaciones realizadas

- Servicio activo:
```bash
systemctl status wazuh-manager
```
Resultado:
```
Active: active (running)
```

- Logs del servicio sin errores críticos:
```bash
tail -n 30 /var/ossec/logs/ossec.log
```

---

## 📍 Estado final del bloque

✔️ Wazuh Manager instalado  
✔️ Servicio activo y funcional  
✔️ Logs iniciales verificados  
✔️ Ambiente listo para instalar el Dashboard en el siguiente bloque

---

## 🗂️ Máquina utilizada: `VM1`