# Bloque 1 – Instalación del Wazuh Manager (Fase 2)

## Objetivo
Instalar y activar el servicio `wazuh-manager` en la VM1 del proyecto SIEM.

## Comandos ejecutados

### 1. Añadir repositorio Wazuh
```bash
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --dearmor > wazuh.gpg
install -o root -g root -m 644 wazuh.gpg /etc/pki/rpm-gpg/
cat > /etc/yum.repos.d/wazuh.repo <<EOF
[wazuh]
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/wazuh.gpg
enabled=1
name=Wazuh repository
baseurl=https://packages.wazuh.com/4.x/yum/
EOF
```

### 2. Instalar wazuh-manager
```bash
dnf install wazuh-manager -y
```

### 3. Habilitar e iniciar el servicio
```bash
systemctl daemon-reexec
systemctl enable wazuh-manager
systemctl start wazuh-manager
```

## Resultado esperado

- Servicio activo:
```bash
systemctl status wazuh-manager
```

- Directorio de instalación: `/var/ossec/`
- Logs disponibles en: `/var/ossec/logs/ossec.log`
