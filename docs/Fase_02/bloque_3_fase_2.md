# Bloque 3 – Hardenización adicional (Fase 2)

## Ajuste de seguridad en fail2ban

### Editar archivo de configuración
```bash
vim /etc/fail2ban/jail.local
```

### Configurar jail personalizado para SSH en puerto 2222
```ini
[sshd]
enabled = true
port = 2222
filter = sshd
logpath = /var/log/secure
maxretry = 3
bantime = 600
```

> 🔐 Puedes aumentar `bantime` a 1800 o más si deseas reforzar la penalización.

### Reiniciar fail2ban
```bash
systemctl restart fail2ban
```

### Validar estado
```bash
fail2ban-client status sshd
```

Debe mostrar algo como:
```
Status for the jail: sshd
|- Filter
|  ...
|- Actions
   |- Currently banned: 1
```

## Documentación final

- Todos los archivos deben estar bajo `docs/fase_2/`
- Comando para subir al repositorio:
```bash
git add docs/fase_2/
git commit -m "Fase 2 completa: instalación de Wazuh Manager, puertos y hardening"
git push origin main
```
